Below is a detailed **Jira User Story** in English for this task.

---

# User Story

**Title:**
Implement JWT-based security for personal and non-personal accounts using Spring Boot Resource Server

**Story Type:**
User Story

**Priority:**
High

**Epic:**
Security / Authorization Framework

---

## Story

**As a** backend microservice,
**I want** to validate incoming JWT tokens and resolve effective roles and privileges for both personal and non-personal accounts,
**so that** access to protected endpoints is enforced consistently and securely based on roles and privileges defined in our system.

---

## Business Context

The microservice receives JWT tokens issued by an external authentication service.
This service is not responsible for issuing tokens, authenticating users, or managing login flows. Its responsibility is to:

* validate incoming JWT tokens
* extract required claims
* build a Spring Security `Authentication`
* resolve effective roles and privileges
* enforce authorization at HTTP and service levels

There are two account types with different token contracts:

1. **Personal account**

   * JWT contains:

     * `userName`
     * `authorities`
   * `authorities` contains **role codes only**
   * privileges are not stored in the token

2. **Non-personal account**

   * JWT contains:

     * `userName`
   * `authorities` claim is absent
   * roles must be resolved from the database

Roles and privileges must be managed in our Oracle database.
Authorization decisions must be based on effective authorities built from both:

* `ROLE_*` authorities
* privilege authorities such as `DOCUMENT_READ`, `DOCUMENT_UPDATE`, etc.

---

## Functional Requirements

### 1. JWT Validation

The service must act as an **OAuth2 Resource Server** and validate JWT tokens issued by an external auth service.

The implementation must:

* accept Bearer tokens
* validate JWT signature and standard token structure
* reject invalid or malformed tokens
* use `issuer-uri` or `jwk-set-uri` configuration

---

### 2. Claims Contract

The service must support the following JWT contract:

#### Personal Account

JWT includes:

* `userName`
* `authorities`

Example:

```json
{
  "userName": "ivan",
  "authorities": ["ADMIN", "EDITOR"]
}
```

#### Non-personal Account

JWT includes:

* `userName`

Example:

```json
{
  "userName": "svc-billing"
}
```

Important rules:

* presence of `authorities` claim means **personal account**
* absence of `authorities` claim means **non-personal account**
* empty `authorities` array is invalid token contract
* `authorities` claim must contain role codes only

---

### 3. Role and Privilege Resolution

The application must resolve effective authorization data as follows:

#### For personal accounts

* extract role codes from JWT `authorities`
* validate that each role exists in the database and is enabled
* load privileges mapped to these roles from the database
* produce final authorities set including:

  * `ROLE_<ROLE_CODE>`
  * privilege codes

#### For non-personal accounts

* resolve service account by `userName`
* load roles assigned to that service account from the database
* validate roles are enabled
* load mapped privileges
* produce final authorities set including:

  * `ROLE_<ROLE_CODE>`
  * privilege codes

---

### 4. Authorization Enforcement

Authorization must be enforced on two levels:

#### HTTP level

Use `SecurityFilterChain` and `requestMatchers(...)` for coarse-grained access control.

Required mappings:

* `GET /api/documents/**` → `DOCUMENT_READ`
* `POST /api/documents/**` → `DOCUMENT_CREATE`
* `PUT /api/documents/**` → `DOCUMENT_UPDATE`
* `PATCH /api/documents/**` → `DOCUMENT_UPDATE`
* `DELETE /api/documents/**` → `DOCUMENT_DELETE`
* `/api/admin/**` → roles `ADMIN` or `OPS`

#### Service level

Use `@EnableMethodSecurity` and `@PreAuthorize` for fine-grained checks in service methods.

Example expectations:

* read methods require `DOCUMENT_READ`
* create methods require `DOCUMENT_CREATE`
* update methods require `DOCUMENT_UPDATE`
* delete methods require `DOCUMENT_DELETE` or admin role

---

### 5. Database Model

Authorization metadata must be stored in **Oracle DB**.

Required tables:

* `privilege`
* `app_role`
* `role_privilege`
* `service_account`
* `service_account_role`

Data model requirements:

* role catalog must be stored in DB
* privilege catalog must be stored in DB
* role-to-privilege mapping must be stored in DB
* non-personal account to role mapping must be stored in DB
* no hardcoded role-to-privilege mapping in Java code

---

### 6. Caching

To reduce repeated database lookups, caching must be implemented for:

* role metadata
* non-personal account authorities by `userName`

Caching requirements:

* use Spring Cache
* use Caffeine
* TTL: 5 minutes

---

### 7. Error Handling

The application must distinguish between authentication and authorization failures.

#### Return 401 Unauthorized when:

* JWT is invalid
* required `userName` claim is missing
* `authorities` claim is malformed
* personal token contains empty role array
* personal token contains unknown or disabled role
* non-personal account is not found
* non-personal account is disabled
* non-personal account has no roles
* role has no active mapped privileges

#### Return 403 Forbidden when:

* token is valid
* authentication is built successfully
* caller lacks required role/privilege for requested resource

Custom handlers must be implemented:

* `AuthenticationEntryPoint`
* `AccessDeniedHandler`

Error response body must be JSON and include:

* timestamp
* status
* error
* message
* path

---

## Technical Requirements

* Java 21
* Spring Boot 3.x
* Spring Security 6
* `spring-boot-starter-oauth2-resource-server`
* `spring-boot-starter-data-jpa`
* Oracle DB
* Oracle JDBC driver
* Flyway
* Spring Cache
* Caffeine
* `spring-boot-starter-validation`

Must use:

* `SecurityFilterChain`
* `@EnableMethodSecurity`
* custom `JwtAuthenticationConverter`
* `@PreAuthorize`

Must not use:

* deprecated Spring Security APIs
* `WebSecurityConfigurerAdapter`
* hardcoded role/privilege mappings in Java

---

## Implementation Notes

The solution must include:

### Security layer

* `SecurityConfig`
* stateless session policy
* resource server JWT configuration
* custom JWT authentication converter
* custom 401/403 handlers

### Security services

* role resolution service
* non-personal account authorities service
* authority normalization utility

### Data layer

* JPA entities for roles, privileges, and service accounts
* repositories with optimized fetching for role/privilege loading

### Database migrations

Flyway migrations must:

* create Oracle-compatible schema
* seed sample roles, privileges, role mappings, and service account mappings

### Demo API

Add sample endpoints to demonstrate the security model:

* document CRUD endpoints
* admin endpoint examples

---

## Acceptance Criteria

### AC1 — Resource Server configuration

**Given** the service starts successfully
**When** a request with a Bearer token reaches a protected endpoint
**Then** the service must validate the token as an OAuth2 Resource Server before any authorization logic is applied

---

### AC2 — Personal account processing

**Given** a JWT contains `userName` and `authorities`
**When** the request is processed
**Then** the service must treat the token as a personal account token
**And** resolve roles from the token against the database
**And** expand them into effective role and privilege authorities

---

### AC3 — Non-personal account processing

**Given** a JWT contains only `userName` and does not contain `authorities`
**When** the request is processed
**Then** the service must treat the token as a non-personal account token
**And** resolve its roles from the database
**And** expand them into effective role and privilege authorities

---

### AC4 — Invalid personal token contract

**Given** a JWT contains `authorities` but the claim is empty, malformed, or contains unknown/disabled roles
**When** the request is processed
**Then** the request must be rejected with HTTP 401

---

### AC5 — Missing userName

**Given** a JWT does not contain `userName`
**When** the request is processed
**Then** the request must be rejected with HTTP 401

---

### AC6 — Unknown non-personal account

**Given** a JWT is valid but the non-personal `userName` is not mapped in the database
**When** the request is processed
**Then** the request must be rejected with HTTP 401

---

### AC7 — HTTP authorization

**Given** a user has insufficient privilege for an endpoint
**When** they call that endpoint
**Then** the request must be rejected with HTTP 403

---

### AC8 — Method-level authorization

**Given** a request passes HTTP-level rules
**When** the target service method is invoked
**Then** method-level `@PreAuthorize` checks must still be enforced

---

### AC9 — Effective authorities

**Given** roles are resolved successfully
**When** the `Authentication` is created
**Then** it must contain:

* `ROLE_*` authorities
* privilege authorities

---

### AC10 — Authentication principal name

**Given** authentication is created successfully
**When** `authentication.getName()` is used
**Then** it must return `userName`

---

### AC11 — Oracle compatibility

**Given** Flyway migrations run on Oracle DB
**When** the application starts
**Then** the schema and seed data must be created successfully using Oracle-compatible SQL

---

### AC12 — Caching

**Given** repeated requests are made for the same non-personal account or role metadata
**When** data has already been loaded recently
**Then** the service should reuse cached authorization data within the configured TTL

---

## Example Seed Data

### Privileges

* `DOCUMENT_READ`
* `DOCUMENT_CREATE`
* `DOCUMENT_UPDATE`
* `DOCUMENT_DELETE`

### Roles

* `ADMIN`
* `EDITOR`
* `VIEWER`
* `OPS`
* `INTEGRATION`

### Role to Privilege Mapping

* `ADMIN` → all document privileges
* `EDITOR` → read, create, update
* `VIEWER` → read
* `OPS` → read, delete
* `INTEGRATION` → read, create

### Service Accounts

* `svc-billing` → `INTEGRATION`
* `svc-reporting` → `VIEWER`
* `svc-ops-cleanup` → `OPS`

---

## Test Scenarios

1. Personal token with `ADMIN`

   * GET document → 200
   * DELETE document → 204

2. Personal token with `VIEWER`

   * GET document → 200
   * DELETE document → 403

3. Non-personal token for `svc-billing`

   * POST document → 201
   * DELETE document → 403

4. Non-personal token for `svc-ops-cleanup`

   * DELETE document → 204

5. Personal token with empty `authorities`

   * any protected request → 401

6. Personal token with unknown role

   * any protected request → 401

7. Token without `userName`

   * any protected request → 401

8. Non-personal token with unknown service account

   * any protected request → 401

---

## Definition of Done

* Security implemented as Spring OAuth2 Resource Server
* Custom JWT-to-Authentication conversion implemented
* Oracle DB schema created with Flyway
* Seed data added
* HTTP-level and service-level authorization implemented
* Cache added for role metadata and non-personal authorities
* Custom JSON 401/403 handlers implemented
* Integration tests added and passing
* Code follows Spring Security 6 modern style
* No hardcoded role-to-privilege mappings in Java
* Documentation added for local setup and flow explanation

---

## Suggested Jira Tasks / Subtasks

1. Configure OAuth2 Resource Server with JWT validation
2. Create Oracle schema and Flyway migrations
3. Implement JPA entities and repositories
4. Implement role-to-privilege resolution service
5. Implement non-personal account authority resolution service
6. Implement custom JWT authentication converter
7. Configure HTTP authorization rules
8. Add method-level security with `@PreAuthorize`
9. Implement custom 401/403 handlers
10. Add caching with Caffeine
11. Create demo endpoints and service methods
12. Add integration tests
13. Document local setup and example flows

If you want, I can also convert this into a **ready-to-paste Jira ticket format** with sections like Summary, Description, Acceptance Criteria, Technical Notes, and Subtasks.
