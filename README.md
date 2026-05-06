AI-Assisted Architecture & Feature Delivery Playbook

1. Executive Summary

This document explains how to work with AI when analyzing an existing microservice architecture or designing and implementing new features.

The core idea: AI must not “invent architecture.” It should be used as an architecture assistant that helps engineers:

* analyze source code, configuration, deployment files, APIs, DTOs, events, and entities;
* extract facts;
* create evidence-based documentation;
* generate E2E flow diagrams;
* assess feature impact;
* identify risks;
* review documentation for hallucinations;
* keep Confluence documentation up to date.

The key rule:

Every AI-generated architectural claim must be traceable to evidence: a file, class, method, config key, endpoint, topic, entity, migration, Docker/Kubernetes/CI/CD artifact, README section, or another provided source.

⸻

2. What Problem This Playbook Solves

Complex backend and microservice systems often suffer from outdated, incomplete, or tribal-knowledge-based architecture documentation.

Problem	Consequence
No up-to-date architecture documentation	New engineers take longer to onboard
Service responsibilities are unclear	Features may be implemented in the wrong service
E2E flows are understood by only a few people	Knowledge silos form
API/event contracts are undocumented	Integrations can be broken accidentally
Data ownership is unclear	Shared database and consistency problems appear
Transaction boundaries are not obvious	Partial failures and duplicate writes become likely
Retry/DLQ/idempotency behavior is not verified	Production incidents become harder to prevent
Security rules are not documented	Endpoint exposure risk increases
Runtime differs from code assumptions	Deployment surprises happen in production
AI may hallucinate	Documentation looks polished but is technically wrong

This playbook introduces a process where AI is used not as a “text generator,” but as an evidence-based architecture analysis tool.

⸻

3. Core Rule: AI Output Is a Draft Until Verified

Any AI-generated architecture document must be treated as a draft until reviewed by engineers.

3.1 What Must Be Verified

Area	What to Verify
Services	Whether the mentioned services exist in the provided files
APIs	Whether endpoints are confirmed by controllers/OpenAPI
Integrations	Whether clients/producers/consumers/routes actually exist
Data	Whether entities/tables/repositories/migrations are confirmed
Messaging	Whether topics/events/producers/consumers are confirmed
Security	Whether Spring Security/auth configuration evidence exists
Deployment	Whether Docker/Kubernetes/Helm/CI/CD evidence exists
Diagrams	Whether every diagram arrow has evidence
Risks	Whether each risk is based on evidence or missing evidence
Unknowns	Whether gaps are explicitly documented

⸻

4. Two Main Usage Scenarios

This approach is useful for two major scenarios.

⸻

4.1 Scenario A — Analyze Existing Architecture

Goal: understand the current system.

Use this scenario when you need to:

* document a legacy system;
* create Confluence architecture documentation;
* understand v1 or v2 architecture;
* discover E2E flows;
* prepare for feature development;
* conduct an architecture review;
* identify production risks;
* onboard new engineers.

4.1.1 Workflow

1. Collect source files.
2. Convert relevant files to .txt if needed.
3. Generate Evidence Base.
4. Generate focused architecture documents.
5. Run Hallucination Review after each document.
6. Correct unsupported claims.
7. Generate Risks and Recommendations.
8. Generate Architecture Overview last.
9. Publish to Confluence.

⸻

4.2 Scenario B — Implement a New Feature

Goal: design and implement a new feature without breaking the existing architecture.

Use this scenario when you need to:

* identify the owning service;
* understand impacted APIs;
* design database changes;
* add an event/topic;
* update an E2E flow;
* assess transaction consistency;
* add idempotency/retry/DLQ handling;
* verify security impact;
* add observability;
* prepare a PR or ADR.

4.2.1 Workflow

1. Read existing architecture documents.
2. Run Feature Impact Analysis prompt.
3. Create Feature Architecture Delta.
4. Validate service ownership, APIs, data, and integrations.
5. Design E2E flow.
6. Design failure handling.
7. Design security and observability.
8. Implement.
9. Use AI-assisted Code Review prompt if needed.
10. Update Confluence documentation.

⸻

5. Why Prompts Are Split

Do not use one large generic prompt like:

Analyze all files and create architecture documentation.

That is risky because AI may:

* compress important details;
* mix API, data, security, runtime, and deployment concerns;
* fill missing gaps with assumptions;
* create polished but unsupported diagrams;
* hide unknowns;
* make claims without evidence.

Instead, use a pack of focused prompts.

Prompt / Document	Purpose
1. Evidence Base	Extract factual evidence from files
2. Service Inventory	Understand services, ownership, and responsibilities
3. API and Integration	Understand REST, messaging, and external integrations
4. Data Architecture	Understand databases, entities, ownership, and transactions
5. E2E Business Flows	Understand flows across the system
6. Deployment Architecture	Understand runtime and deployment topology
7. Security Architecture	Understand authentication, authorization, and secrets
8. Resilience/Observability	Understand production readiness
9. Risks and Recommendations	Consolidate architecture risks
10. Architecture Overview	Create a high-level summary
11. Hallucination Review	Review unsupported claims
12. Feature Impact Analysis	Assess a new feature before implementation
13. Feature Architecture Delta	Document proposed feature-level architecture changes

⸻

6. Recommended Confluence Structure

Architecture Documentation
├── 01. AI-Assisted Architecture & Feature Delivery Playbook
├── 02. Evidence Base
├── 03. Architecture Overview
├── 04. Service Inventory and Ownership
├── 05. API and Integration Architecture
├── 06. Data Architecture
├── 07. End-to-End Business Flows
├── 08. Deployment and Runtime Architecture
├── 09. Security Architecture
├── 10. Resilience, Reliability and Observability Review
├── 11. Architecture Risks, Unknowns and Recommendations
└── 12. Feature Architecture Deltas
    ├── Feature Delta — <feature-name-1>
    ├── Feature Delta — <feature-name-2>
    └── Feature Delta — <feature-name-3>

⸻

7. How to Prepare Input for AI

7.1 Files to Provide

For high-quality analysis, provide more than Java code. Include configuration, deployment, documentation, and tests where possible.

Area	Files
Build	pom.xml, build.gradle
Configuration	application.yml, application.properties
API	controllers, OpenAPI, DTOs
Business logic	services, validators, mappers
Persistence	entities, repositories, migrations
Messaging	producers, consumers, event classes, Kafka config
Security	security config, filters, auth classes
Deployment	Dockerfile, docker-compose, Kubernetes, Helm
CI/CD	GitHub Actions, Jenkinsfile, GitLab CI
Documentation	README, ADRs, existing Confluence exports
Tests	integration tests, contract tests, E2E tests

7.2 File Naming

Good file names:

v1-order-service-controller.txt
v1-order-service-service-layer.txt
v1-order-service-application-yml.txt
v1-order-service-kafka-consumer.txt
v1-payment-service-entities.txt
v1-docker-compose.txt
v1-kubernetes-manifests.txt

Poor file names:

file1.txt
code.txt
new.txt
misc.txt

AI performs better when file names carry architectural context.

⸻

8. Prompt Pack

The following prompts form the operational toolkit for this playbook.

⸻

8.1 Prompt 1 — Evidence Base

When to use: always first when analyzing existing architecture.

You are acting as a Senior Software Architect and Technical Documentation Engineer.
I will provide you with multiple .txt files. These files may contain source code, configuration files, README content, Docker/Kubernetes manifests, CI/CD pipelines, dependency files, API definitions, database schemas, Kafka configuration, security configuration, or other architecture-related materials from different microservices.
Your task is to create a FACT-BASED EVIDENCE BASE from the provided files.
This is NOT the final architecture document yet.
Do NOT summarize too much.
Do NOT invent anything.
Extract only facts that are directly visible in the provided files.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY information from the provided .txt files.
2. Do NOT invent services, APIs, databases, queues, topics, dependencies, protocols, flows, infrastructure, business processes, or deployment topology.
3. If something is not explicitly present, write:
   “Not found in provided files”.
4. If something is only weakly inferred from naming, write:
   “Inferred from naming/structure — not confirmed”.
5. Every important claim must include evidence:
   - source file name
   - class/config/key/section/function/method name if available
   - confidence level
6. Do NOT use generic microservice knowledge unless it is directly supported by the files.
7. Do NOT assume relationships between services unless there is evidence:
   - HTTP client
   - Feign/WebClient/RestTemplate
   - Kafka producer/consumer/topic
   - config property
   - Docker/Kubernetes dependency
   - README statement
   - gateway route
8. If evidence conflicts between files, explicitly mark it as:
   “Conflicting evidence”.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT STRUCTURE
================================================================================
# Evidence Base
## 1. Provided File Inventory
| File name | Detected service/module | Content type | Main technologies | Important facts found | Confidence |
|----------|--------------------------|--------------|-------------------|-----------------------|------------|
## 2. Detected Services / Modules
| Service/module | Evidence file(s) | Why this is considered a service/module | Confidence |
|---------------|------------------|------------------------------------------|------------|
## 3. Detected Technologies
| Technology | Where found | Exact evidence | Usage context | Confidence |
|------------|-------------|----------------|---------------|------------|
## 4. Detected REST APIs
| Service | HTTP method | Path | Controller/class | Request DTO | Response DTO | Auth/security evidence | Source file | Confidence |
|--------|-------------|------|------------------|-------------|--------------|------------------------|-------------|------------|
## 5. Detected Service-to-Service Calls
| Source service | Target service/system | Mechanism | Endpoint/config/client | Purpose | Evidence | Confidence |
|---------------|------------------------|-----------|------------------------|---------|----------|------------|
## 6. Detected Messaging
| Service | Direction | Broker | Topic/queue | Producer/consumer class | Message type | Retry/DLQ evidence | Source file | Confidence |
|--------|-----------|--------|-------------|-------------------------|--------------|--------------------|-------------|------------|
## 7. Detected Persistence
| Service | Database technology | Entities/tables | Repository/DAO | Migrations | Transaction evidence | Source file | Confidence |
|--------|---------------------|-----------------|----------------|------------|----------------------|-------------|------------|
## 8. Detected Security Facts
| Service | Security mechanism | Class/config | Protected endpoints | Public endpoints | Roles/permissions | Evidence | Confidence |
|--------|--------------------|--------------|---------------------|------------------|-------------------|----------|------------|
## 9. Detected Deployment / Runtime Facts
| Component | Deployment artifact | Port | Environment variables | Runtime dependencies | Health checks | Evidence | Confidence |
|----------|---------------------|------|-----------------------|----------------------|---------------|----------|------------|
## 10. Detected Observability Facts
| Service/component | Logging | Metrics | Tracing | Health checks | Correlation ID | Evidence | Confidence |
|------------------|---------|---------|---------|---------------|----------------|----------|------------|
## 11. Confirmed Unknowns
| Unknown area | What is missing | Why it matters | Suggested file/source to verify |
|-------------|-----------------|----------------|--------------------------------|
## 12. Evidence Mapping
| Claim | Evidence source | Exact class/config/key/section | Confidence |
|-------|-----------------|--------------------------------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Analyze the provided .txt files and produce only the Evidence Base document.
Do not create high-level architecture conclusions yet.
Do not create diagrams unless the structure is directly confirmed.

⸻

8.2 Prompt 2 — Service Inventory and Ownership

When to use: after Evidence Base, to understand service boundaries.

You are acting as a Senior Software Architect and Staff Backend Engineer.
I will provide you with multiple .txt files and optionally an Evidence Base document.
Your task is to create a Confluence-ready document called:
“Service Inventory and Ownership”
The document must describe all detected microservices/modules and their responsibilities based only on evidence from the provided files.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY facts from the provided files and/or Evidence Base.
2. Do NOT invent services, modules, APIs, databases, queues, topics, dependencies, owners, responsibilities, or business capabilities.
3. If a responsibility is not directly visible, write:
   “Unknown / Not confirmed from provided files”.
4. Clearly separate:
   - Confirmed from code/configuration
   - Inferred from naming/structure
   - Unknown / not confirmed
5. Every important claim must include evidence:
   - source file
   - class/config/key/section where possible
   - confidence level
6. If service ownership of data is not explicit, do NOT assume it. Mark it as unknown.
7. If there are conflicting signals, mark them as “Conflicting evidence”.
8. Prefer incomplete but accurate documentation over complete but speculative documentation.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# Service Inventory and Ownership
## 1. Purpose of This Document
## 2. Analysis Scope
| Area | Status | Evidence |
|------|--------|----------|
## 3. Service Inventory Summary
| Service/module | Purpose | Main responsibilities | Exposed APIs | Messaging | Database/data ownership | External dependencies | Evidence | Confidence |
|---------------|---------|----------------------|--------------|-----------|-------------------------|-----------------------|----------|------------|
## 4. Detailed Service Descriptions
For each detected service/module:
### 4.x Service: <service-name>
#### 4.x.1 Confirmed Purpose
| Claim | Evidence | Confidence |
|-------|----------|------------|
#### 4.x.2 Responsibilities
| Responsibility | Confirmed / Inferred / Unknown | Evidence | Confidence |
|---------------|---------------------------------|----------|------------|
#### 4.x.3 Important Classes / Packages / Files
| Class/package/file | Role | Evidence | Confidence |
|-------------------|------|----------|------------|
#### 4.x.4 Exposed APIs
| Method | Path | Controller | Request DTO | Response DTO | Purpose | Auth evidence | Evidence | Confidence |
|-------|------|------------|-------------|--------------|---------|---------------|----------|------------|
#### 4.x.5 Consumed APIs / External Calls
| Target service/system | Mechanism | Endpoint/client/config | Purpose | Evidence | Confidence |
|----------------------|-----------|------------------------|---------|----------|------------|
#### 4.x.6 Messaging
| Direction | Broker | Topic/queue | Producer/consumer | Message type | Purpose | Retry/DLQ evidence | Evidence | Confidence |
|----------|--------|-------------|-------------------|--------------|---------|--------------------|----------|------------|
#### 4.x.7 Persistence and Data Ownership
| Database/entity/table | Owned by this service? | Repository/DAO | Transaction evidence | Migration evidence | Evidence | Confidence |
|----------------------|------------------------|----------------|----------------------|--------------------|----------|------------|
#### 4.x.8 Configuration Summary
| Config area | Value/finding | Source | Confidence |
|------------|---------------|--------|------------|
#### 4.x.9 Known Unknowns for This Service
| Unknown | Why it matters | Suggested verification |
|---------|----------------|------------------------|
## 5. Cross-Service Ownership Matrix
| Capability/data area | Owning service | Other services depending on it | Evidence | Confidence |
|---------------------|----------------|--------------------------------|----------|------------|
## 6. Service Dependency Matrix
| Source service | Depends on | Dependency type | Evidence | Confidence |
|---------------|------------|-----------------|----------|------------|
## 7. Risks Identified from Service Inventory
| Risk | Evidence | Impact | Recommendation | Confidence |
|------|----------|--------|----------------|------------|
## 8. Evidence Mapping
| Claim | Source file | Class/config/key/section | Confidence |
|-------|-------------|--------------------------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready “Service Inventory and Ownership” document.
Base every conclusion on facts.
Do not hallucinate.
Mark missing information explicitly.

⸻

8.3 Prompt 3 — API and Integration Architecture

When to use: to document REST APIs, HTTP clients, Kafka, gateway routes, and external integrations.

You are acting as a Senior Software Architect and Integration Architecture Reviewer.
I will provide you with multiple .txt files and optionally Evidence Base and Service Inventory documents.
Your task is to create a Confluence-ready document called:
“API and Integration Architecture”
This document must describe synchronous and asynchronous integrations between services and external systems.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY facts from the provided files and/or provided evidence documents.
2. Do NOT invent endpoints, clients, topics, queues, payloads, services, or integrations.
3. Do NOT assume that services communicate unless there is direct evidence:
   - controller route
   - HTTP client
   - Feign/WebClient/RestTemplate
   - Kafka/RabbitMQ/PubSub producer/consumer
   - topic/queue config
   - gateway route
   - Docker/K8s dependency
   - README statement
4. If target service is unknown, write:
   “Target unknown / not confirmed from provided files”.
5. If payload is not visible, write:
   “Payload not found in provided files”.
6. If retry/DLQ/timeout behavior is not visible, write:
   “Not confirmed from provided files”.
7. Every important claim must include evidence.
8. Clearly separate confirmed integrations from inferred ones.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# API and Integration Architecture
## 1. Purpose of This Document
## 2. Integration Scope
| Integration area | Status | Evidence |
|------------------|--------|----------|
## 3. High-Level Integration Diagram
Create a Mermaid diagram only with confirmed or strongly inferred integrations.
After the diagram:
| Source | Target | Mechanism | Purpose | Evidence | Confidence |
|--------|--------|-----------|---------|----------|------------|
## 4. Exposed REST APIs
| Method | Path | Controller | Request DTO | Response DTO | Purpose | Auth evidence | Evidence | Confidence |
|-------|------|------------|-------------|--------------|---------|---------------|----------|------------|
## 5. Consumed Synchronous APIs
| Source service | Target service/system | Client mechanism | Endpoint/config | Request payload | Response payload | Purpose | Evidence | Confidence |
|---------------|------------------------|------------------|-----------------|-----------------|------------------|---------|----------|------------|
## 6. API Gateway / Routing
| Route ID/name | Incoming path | Target service/URI | Filters | Predicates | Security | Evidence | Confidence |
|--------------|---------------|--------------------|---------|------------|----------|----------|------------|
## 7. Asynchronous Messaging Architecture
| Source service | Target service/consumer | Broker | Topic/queue | Producer | Consumer | Message type | Purpose | Evidence | Confidence |
|---------------|-------------------------|--------|-------------|----------|----------|--------------|---------|----------|------------|
## 8. Event / Message Payloads
| Message/event type | Producer | Consumer | Fields visible | Purpose | Schema/versioning evidence | Evidence | Confidence |
|-------------------|----------|----------|----------------|---------|----------------------------|----------|------------|
## 9. Reliability Mechanisms for Integrations
| Integration | Timeout | Retry | Backoff | Circuit breaker | DLQ | Idempotency | Evidence | Confidence |
|------------|---------|-------|---------|-----------------|-----|-------------|----------|------------|
## 10. Integration Failure Scenarios
| Scenario | Current behavior from code/config | Risk | Recommendation | Evidence |
|---------|-----------------------------------|------|----------------|----------|
## 11. Integration Risks
| Risk | Evidence | Impact | Recommendation | Confidence |
|------|----------|--------|----------------|------------|
## 12. Evidence Mapping
| Claim | Source file | Class/config/key/section | Confidence |
|-------|-------------|--------------------------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready “API and Integration Architecture” document.
Use only facts from the files.
Do not hallucinate.
Mark all missing or uncertain information explicitly.

⸻

8.4 Prompt 4 — Data Architecture

When to use: to analyze databases, entities, ownership, transactions, and migrations.

You are acting as a Senior Software Architect and Data Architecture Reviewer.
I will provide you with multiple .txt files and optionally Evidence Base, Service Inventory, and Integration documents.
Your task is to create a Confluence-ready document called:
“Data Architecture”
This document must describe entities, tables, repositories, database ownership, transaction boundaries, consistency model, and data risks.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY facts from the provided files and/or provided evidence documents.
2. Do NOT invent entities, tables, columns, relationships, databases, indexes, migrations, or ownership.
3. Do NOT assume a database exists unless there is evidence:
   - entity classes
   - repositories/DAOs
   - SQL/Flyway/Liquibase migrations
   - application database config
   - Docker/K8s DB dependency
   - README statement
4. Do NOT assume relationships unless annotations, foreign keys, migrations, or code clearly show them.
5. Do NOT assume data ownership only because a service reads or writes a table.
6. If ownership is not explicit, write:
   “Ownership unknown / not confirmed from provided files”.
7. If database type is not visible, write:
   “Database type unknown”.
8. Every important claim must include evidence.
9. Prefer missing details over speculative ER diagrams.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# Data Architecture
## 1. Purpose of This Document
## 2. Data Scope
| Data area | Status | Evidence |
|----------|--------|----------|
## 3. Detected Databases
| Service | Database technology | Connection/config evidence | Runtime/deployment evidence | Confidence |
|--------|---------------------|----------------------------|-----------------------------|------------|
## 4. Entity / Table Inventory
| Service | Entity/table | Purpose | Important fields | Relationships | Evidence | Confidence |
|--------|--------------|---------|------------------|---------------|----------|------------|
## 5. Repository / DAO Layer
| Service | Repository/DAO | Entity/table | Query methods/custom SQL | Purpose | Evidence | Confidence |
|--------|----------------|--------------|--------------------------|---------|----------|------------|
## 6. Database Ownership
| Data/entity/table | Confirmed owner service | Other services accessing it | Ownership evidence | Confidence |
|------------------|-------------------------|-----------------------------|-------------------|------------|
## 7. Transaction Boundaries
| Service | Operation/method | Transaction boundary | Isolation/propagation | Read/write behavior | Evidence | Confidence |
|--------|------------------|----------------------|-----------------------|---------------------|----------|------------|
## 8. Locking and Concurrency
| Service | Entity/table | Locking mechanism | Use case | Risk | Evidence | Confidence |
|--------|--------------|-------------------|----------|------|----------|------------|
## 9. Migrations and Schema Evolution
| Service | Migration tool | Migration files | Schema changes found | Rollback/versioning evidence | Confidence |
|--------|----------------|-----------------|----------------------|------------------------------|------------|
## 10. Indexes and Constraints
| Table/entity | Index/constraint | Purpose | Evidence | Confidence |
|-------------|------------------|---------|----------|------------|
## 11. Consistency Model
| Flow/data area | Consistency model | Evidence | Risk | Confidence |
|---------------|------------------|----------|------|------------|
## 12. ER Diagram
Create Mermaid ER diagram only if enough information exists.
## 13. Data Risks
| Risk | Evidence | Impact | Recommendation | Confidence |
|------|----------|--------|----------------|------------|
## 14. Evidence Mapping
| Claim | Source file | Class/config/key/section | Confidence |
|-------|-------------|--------------------------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready “Data Architecture” document.
Use only facts from the files.
Do not hallucinate.
If there is not enough data for ERD or ownership, say so explicitly.

⸻

8.5 Prompt 5 — End-to-End Business Flows

When to use: to describe E2E flows and generate sequence diagrams.

You are acting as a Senior Software Architect and System Flow Analyst.
I will provide you with multiple .txt files and optionally Evidence Base, Service Inventory, API/Integration, and Data Architecture documents.
Your task is to create a Confluence-ready document called:
“End-to-End Business Flows”
This document must describe the most important confirmed business/application flows visible in the provided files.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY facts from the provided files and/or provided evidence documents.
2. Do NOT invent business flows.
3. A flow can be documented only if there is evidence such as:
   - REST endpoint
   - controller/service method chain
   - Kafka producer/consumer
   - repository/database operation
   - event model
   - README flow description
   - gateway route
4. If a step is missing, write:
   “Step unknown / not confirmed from provided files”.
5. Do NOT assume happy path or failure behavior unless visible in code/config.
6. Do NOT invent sequence diagram participants.
7. Every step must include evidence.
8. If the full flow cannot be reconstructed, document only the confirmed partial flow.
9. Clearly mark:
   - Confirmed steps
   - Inferred steps
   - Unknown/missing steps
10. Prefer partial but accurate flows over complete speculative diagrams.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# End-to-End Business Flows
## 1. Purpose of This Document
## 2. Flow Discovery Summary
| Flow candidate | Trigger evidence | Services involved | Confirmed completeness | Confidence |
|---------------|------------------|-------------------|------------------------|------------|
## 3. High-Level Flow Map
Create Mermaid diagram only if there are confirmed flows.
## 4. Detailed Flow Sections
For each confirmed or partially confirmed flow:
### 4.x Flow: <flow-name>
#### 4.x.1 Flow Status
| Attribute | Value |
|----------|-------|
#### 4.x.2 Trigger
#### 4.x.3 Preconditions
| Precondition | Type | Evidence | Confidence |
|-------------|------|----------|------------|
#### 4.x.4 Step-by-Step Flow
| Step | Actor/component | Action | Input | Output | Evidence | Confidence |
|------|-----------------|--------|-------|--------|----------|------------|
#### 4.x.5 Sequence Diagram
Create a Mermaid sequence diagram using only confirmed participants.
#### 4.x.6 Data Movement
| Data object | Type | Source | Target | Important fields | Evidence | Confidence |
|------------|------|--------|--------|------------------|----------|------------|
#### 4.x.7 Transaction Boundaries
| Step/method | Transaction behavior | Evidence | Risk |
|------------|----------------------|----------|------|
#### 4.x.8 State Changes
| Entity/message/status | Before | After | Where changed | Evidence | Confidence |
|----------------------|--------|-------|---------------|----------|------------|
#### 4.x.9 Failure Behavior
| Failure scenario | Current behavior from files | Evidence | Risk | Recommendation |
|-----------------|-----------------------------|----------|------|----------------|
#### 4.x.10 Open Questions for This Flow
| Question | Why it matters | Suggested verification |
|----------|----------------|------------------------|
## 5. Cross-Flow Risks
| Risk | Affected flow(s) | Evidence | Impact | Recommendation |
|------|------------------|----------|--------|----------------|
## 6. Evidence Mapping
| Claim | Source file | Class/config/key/section | Confidence |
|-------|-------------|--------------------------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready “End-to-End Business Flows” document.
Only document flows supported by evidence.
Do not invent missing steps.
Mark unknowns explicitly.

⸻

8.6 Prompt 6 — Deployment and Runtime Architecture

When to use: for Docker, Kubernetes, Helm, CI/CD, runtime configuration, and operational topology.

You are acting as a Senior Platform Engineer, DevOps Architect, and Software Architect.
I will provide you with multiple .txt files and optionally Evidence Base, Service Inventory, API/Integration, and Data Architecture documents.
Your task is to create a Confluence-ready document called:
“Deployment and Runtime Architecture”
This document must describe how the system appears to be built, configured, deployed, and executed based only on the provided files.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY facts from the provided files and/or provided evidence documents.
2. Do NOT invent environments, clusters, namespaces, Docker images, ports, secrets, config maps, ingress, service discovery, CI/CD stages, or deployment topology.
3. Deployment architecture can be described only if there is evidence:
   - Dockerfile
   - docker-compose
   - Kubernetes manifests
   - Helm charts
   - CI/CD pipeline
   - application configuration
   - README deployment instructions
4. If deployment files are missing, explicitly write:
   “Deployment architecture is not confirmed from provided files”.
5. Do NOT assume Kubernetes exists unless manifests/Helm/README/pipeline prove it.
6. Do NOT assume Docker exists unless Dockerfile/Compose/pipeline/README prove it.
7. Do NOT assume production/staging/dev environments unless visible in files.
8. Every important claim must include evidence.
9. Mark inferred runtime dependencies separately from confirmed ones.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# Deployment and Runtime Architecture
## 1. Purpose of This Document
## 2. Deployment Evidence Summary
| Artifact type | Found / Not found | File(s) | Important findings | Confidence |
|--------------|-------------------|---------|--------------------|------------|
## 3. Runtime Components
| Component | Type | Runtime artifact | Port | Environment variables | Depends on | Evidence | Confidence |
|----------|------|------------------|------|-----------------------|------------|----------|------------|
## 4. Runtime Topology Diagram
Create Mermaid diagram only if enough evidence exists.
## 5. Docker Architecture
| Service | Image/build context | Dockerfile | Ports | Volumes | Environment variables | Health check | Dependencies | Evidence |
|--------|---------------------|------------|-------|---------|-----------------------|--------------|--------------|----------|
## 6. Kubernetes / Helm Architecture
| Component | Kind | Namespace | Image | Ports | ConfigMaps | Secrets | Probes | Resources | Autoscaling | Evidence |
|----------|------|-----------|-------|-------|------------|---------|--------|-----------|-------------|----------|
## 7. Configuration Management
| Service/component | Config source | Key/value or category | Purpose | Sensitive? | Evidence | Confidence |
|------------------|---------------|-----------------------|---------|------------|----------|------------|
## 8. Profiles and Environments
| Profile/environment | Services affected | Configuration differences | Evidence | Confidence |
|--------------------|-------------------|---------------------------|----------|------------|
## 9. Networking and Service Discovery
| Source | Target | Mechanism | Port/URL/service name | Evidence | Confidence |
|--------|--------|-----------|-----------------------|----------|------------|
## 10. Health Checks and Operational Readiness
| Component | Health/readiness/liveness mechanism | Endpoint/config | Used by | Evidence | Confidence |
|----------|--------------------------------------|-----------------|---------|----------|------------|
## 11. CI/CD Pipeline
| Pipeline/stage | Trigger | Build steps | Test steps | Image publishing | Deployment step | Secrets used | Evidence |
|---------------|---------|------------|------------|------------------|-----------------|--------------|----------|
## 12. Runtime Risks
| Risk | Evidence | Impact | Recommendation | Confidence |
|------|----------|--------|----------------|------------|
## 13. Evidence Mapping
| Claim | Source file | Class/config/key/section | Confidence |
|-------|-------------|--------------------------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready “Deployment and Runtime Architecture” document.
Use only deployment/runtime evidence from files.
Do not invent infrastructure.
Mark missing deployment information explicitly.

⸻

8.7 Prompt 7 — Security Architecture

When to use: for authentication, authorization, JWT, secrets, CORS/CSRF, actuator exposure, and endpoint protection.

You are acting as a Senior Application Security Architect and Staff Backend Engineer.
I will provide you with multiple .txt files and optionally Evidence Base, Service Inventory, API/Integration, Data, and Deployment documents.
Your task is to create a Confluence-ready document called:
“Security Architecture”
This document must describe authentication, authorization, endpoint protection, service-to-service security, secrets handling, and visible security risks.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY facts from the provided files and/or provided evidence documents.
2. Do NOT invent authentication mechanisms, authorization models, roles, permissions, security filters, OAuth flows, JWT claims, or service-to-service security.
3. Security behavior can be described only if there is evidence:
   - Spring Security configuration
   - filters
   - annotations like @PreAuthorize
   - gateway security config
   - JWT/resource server config
   - application config
   - README/security docs
   - tests verifying security behavior
4. If security is missing or unclear, write:
   “Security not confirmed from provided files”.
5. Do NOT assume endpoints are protected unless there is evidence.
6. Do NOT assume JWT/OAuth/session behavior unless visible.
7. Do NOT assume secrets are safe if they are shown in plain text.
8. Every important claim must include evidence.
9. Clearly separate confirmed security behavior from unknowns.
10. Be strict and production-oriented.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# Security Architecture
## 1. Purpose of This Document
## 2. Security Evidence Summary
| Security area | Found / Not found | Evidence | Confidence |
|--------------|-------------------|----------|------------|
## 3. Authentication Model
| Service/component | Authentication mechanism | Config/class | Token/session details | Evidence | Confidence |
|------------------|--------------------------|--------------|----------------------|----------|------------|
## 4. Authorization Model
| Service/component | Authorization mechanism | Roles/permissions | Protected resource | Evidence | Confidence |
|------------------|-------------------------|-------------------|--------------------|----------|------------|
## 5. Endpoint Protection Matrix
| Service | Endpoint/path | Public/protected/unknown | Required role/authority | Evidence | Confidence |
|--------|---------------|--------------------------|-------------------------|----------|------------|
## 6. JWT / Token Handling
| Service | JWT role | Decoder/validator/filter | Claims used | Authorities mapping | Evidence | Confidence |
|--------|----------|--------------------------|-------------|--------------------|----------|------------|
## 7. Service-to-Service Security
| Source | Target | Security mechanism | Evidence | Risk |
|--------|--------|--------------------|----------|------|
## 8. Secrets and Sensitive Configuration
| Secret/config | Location | Appears sensitive? | Stored securely? | Risk | Evidence |
|--------------|----------|-------------------|------------------|------|----------|
## 9. CORS, CSRF, Sessions
| Service | CORS | CSRF | Session management | Evidence | Risk |
|--------|------|------|-------------------|----------|------|
## 10. Actuator / Admin / Internal Endpoint Exposure
| Service | Endpoint/config | Exposure level | Security evidence | Risk |
|--------|-----------------|----------------|-------------------|------|
## 11. Security Risks
| Severity | Risk | Evidence | Impact | Recommendation |
|----------|------|----------|--------|----------------|
## 12. Evidence Mapping
| Claim | Source file | Class/config/key/section | Confidence |
|-------|-------------|--------------------------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready “Security Architecture” document.
Use only evidence from files.
Do not hallucinate security behavior.
If security is unclear, mark it as unknown.

⸻

8.8 Prompt 8 — Resilience, Reliability and Observability Review

When to use: to assess production readiness.

You are acting as a Senior Staff Backend Engineer, SRE-minded Architect, and Production Readiness Reviewer.
I will provide you with multiple .txt files and optionally Evidence Base, Service Inventory, API/Integration, Data, Deployment, and Security documents.
Your task is to create a Confluence-ready document called:
“Resilience, Reliability and Observability Review”
This document must assess production readiness based only on visible evidence.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY facts from the provided files and/or provided evidence documents.
2. Do NOT invent resilience mechanisms.
3. Do NOT assume retries, timeouts, DLQ, circuit breakers, idempotency, outbox, metrics, tracing, or logging unless visible in files.
4. If a mechanism is missing or not visible, write:
   “Not confirmed from provided files”.
5. Every claim must include evidence.
6. Be strict. Missing production controls should be treated as risks, but clearly state that absence is based on provided files.
7. Do NOT claim that something is definitely absent from the real system. Say:
   “Not found in provided files”.
8. Separate:
   - Confirmed mechanisms
   - Missing/not found mechanisms
   - Risks
   - Recommendations
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# Resilience, Reliability and Observability Review
## 1. Purpose of This Document
## 2. Review Scope
| Area | Found / Not found | Evidence | Confidence |
|------|-------------------|----------|------------|
## 3. Resilience Mechanisms
| Mechanism | Service/component | Current evidence | Configuration/details | Confidence |
|----------|-------------------|------------------|-----------------------|------------|
## 4. Reliability Review by Service
| Concern | Current evidence | Risk level | Impact | Recommendation |
|---------|------------------|------------|--------|----------------|
## 5. Messaging Reliability Review
| Topic/queue | Producer | Consumer | Retry | DLQ | Ordering | Duplicate handling | Poison message handling | Evidence | Risk |
|------------|----------|----------|-------|-----|----------|--------------------|-------------------------|----------|------|
## 6. HTTP / API Reliability Review
| Source | Target | Timeout | Retry | Circuit breaker | Fallback | Error handling | Evidence | Risk |
|--------|--------|---------|-------|-----------------|----------|----------------|----------|------|
## 7. Transaction and Consistency Reliability
| Flow/service | Transaction evidence | Consistency risk | Outbox/idempotency evidence | Recommendation |
|-------------|----------------------|------------------|-----------------------------|----------------|
## 8. Health Checks and Operational Readiness
| Component | Health check | Readiness | Liveness | Startup dependency behavior | Evidence | Risk |
|----------|--------------|-----------|----------|-----------------------------|----------|------|
## 9. Observability Review
| Observability area | Current evidence | Missing / not confirmed | Risk | Recommendation |
|-------------------|------------------|-------------------------|------|----------------|
## 10. Failure Mode Analysis
| Failure mode | Current behavior from files | Risk level | Impact | Recommendation | Evidence |
|-------------|-----------------------------|------------|--------|----------------|----------|
## 11. Production Readiness Scorecard
| Category | Score 0-5 | Reason | Evidence |
|----------|-----------|--------|----------|
## 12. Recommendations
### Critical recommendations
### High-priority recommendations
### Medium-priority recommendations
## 13. Evidence Mapping
| Claim | Source file | Class/config/key/section | Confidence |
|-------|-------------|--------------------------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready “Resilience, Reliability and Observability Review” document.
Use only facts from the files.
Do not invent production mechanisms.
Mark missing mechanisms as “Not found in provided files”.

⸻

8.9 Prompt 9 — Architecture Risks, Unknowns and Recommendations

When to use: after all focused architecture documents are created.

You are acting as a Principal Software Architect and Architecture Review Board member.
I will provide you with multiple .txt files and optionally all previously generated documents.
Your task is to create a Confluence-ready document called:
“Architecture Risks, Unknowns and Recommendations”
This document must consolidate risks and open questions across all architecture areas.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY facts from the provided files and/or previous evidence-based documents.
2. Do NOT invent risks that are not connected to evidence or missing evidence.
3. Do NOT claim that something is definitely absent from the real system. Say:
   “Not found in provided files”.
4. Every risk must include:
   - evidence or missing evidence
   - impact
   - recommendation
   - severity
5. Do NOT exaggerate severity without justification.
6. Clearly distinguish:
   - confirmed problems
   - potential risks due to missing evidence
   - open questions
7. Recommendations must be practical and implementation-oriented.
8. Do NOT rewrite previous documents. Consolidate findings.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# Architecture Risks, Unknowns and Recommendations
## 1. Executive Risk Summary
## 2. Risk Classification Method
| Severity | Meaning |
|----------|---------|
## 3. Critical Risks
| ID | Risk | Evidence / missing evidence | Impact | Recommendation | Affected services/flows | Confidence |
|----|------|-----------------------------|--------|----------------|-------------------------|------------|
## 4. High Risks
| ID | Risk | Evidence / missing evidence | Impact | Recommendation | Affected services/flows | Confidence |
|----|------|-----------------------------|--------|----------------|-------------------------|------------|
## 5. Medium Risks
| ID | Risk | Evidence / missing evidence | Impact | Recommendation | Affected services/flows | Confidence |
|----|------|-----------------------------|--------|----------------|-------------------------|------------|
## 6. Low Risks
| ID | Risk | Evidence / missing evidence | Impact | Recommendation | Affected services/flows | Confidence |
|----|------|-----------------------------|--------|----------------|-------------------------|------------|
## 7. Unknowns and Open Questions
| ID | Open question | Why it matters | Where to verify | Priority |
|----|---------------|----------------|-----------------|----------|
## 8. Recommended Next Steps
### Immediate next steps
### Short-term improvements
### Medium-term improvements
## 9. Architecture Decision Candidates
| ADR candidate | Problem to solve | Options to evaluate | Why this matters |
|--------------|------------------|---------------------|------------------|
## 10. Production Readiness Summary
| Area | Current evidence level | Risk level | Recommendation |
|------|------------------------|------------|----------------|
## 11. Evidence Mapping
| Risk/claim | Source document/file | Evidence | Confidence |
|------------|----------------------|----------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready “Architecture Risks, Unknowns and Recommendations” document.
Base every risk on evidence or explicitly missing evidence.
Do not hallucinate.
Do not overstate certainty.

⸻

8.10 Prompt 10 — Architecture Overview

When to use: last, after the detailed documents are ready.

You are acting as a Principal Software Architect and Technical Documentation Engineer.
I will provide you with multiple .txt files and optionally all previously generated architecture documents.
Your task is to create a Confluence-ready high-level document called:
“Architecture Overview”
This document must summarize the system architecture clearly and accurately for engineers, architects, tech leads, and managers.
================================================================================
CRITICAL ANTI-HALLUCINATION RULES
================================================================================
1. Use ONLY facts from the provided files and/or previous evidence-based documents.
2. Do NOT invent services, flows, databases, integrations, deployment topology, or business capabilities.
3. If something is unknown, write:
   “Unknown / Not confirmed from provided files”.
4. Every major architectural statement must be traceable to evidence.
5. Do NOT include low-level implementation details unless needed for understanding the architecture.
6. Do NOT overstate certainty.
7. Do NOT hide unknowns.
8. Do NOT present inferred components as confirmed.
9. This document should link conceptually to the detailed documents, not duplicate all details.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# Architecture Overview
## 1. Executive Summary
## 2. System Scope
| Area | Included / Not confirmed | Notes | Evidence |
|------|--------------------------|-------|----------|
## 3. Detected Services
| Service/module | Purpose | Main responsibilities | Main dependencies | Confidence |
|---------------|---------|----------------------|-------------------|------------|
## 4. High-Level System Context Diagram
Create a Mermaid diagram using only confirmed or strongly inferred components.
After the diagram:
| Source | Target | Mechanism | Purpose | Confidence |
|--------|--------|-----------|---------|------------|
## 5. High-Level Architecture Description
Explain:
- service boundaries
- communication style
- data ownership
- deployment/runtime model
- security model
- observability model
## 6. Main Business / Application Flows
| Flow | Trigger | Services involved | Completeness | Confidence |
|------|---------|-------------------|--------------|------------|
## 7. Main Integrations
| Source | Target | Type | Mechanism | Evidence level | Confidence |
|--------|--------|------|-----------|----------------|------------|
## 8. Data Architecture Summary
| Data area/entity | Owning service | Database | Consistency model | Confidence |
|------------------|----------------|----------|-------------------|------------|
## 9. Deployment Summary
| Component | Runtime/deployment evidence | Environment/profile | Confidence |
|----------|-----------------------------|---------------------|------------|
## 10. Security Summary
| Security area | Current evidence | Risk/unknown | Confidence |
|--------------|------------------|--------------|------------|
## 11. Reliability and Observability Summary
| Area | Current evidence | Risk/unknown | Recommendation |
|------|------------------|--------------|----------------|
## 12. Key Architecture Risks
| Severity | Risk | Impact | Recommendation |
|----------|------|--------|----------------|
## 13. Unknowns and Open Questions
| Question | Why it matters | Where to verify |
|----------|----------------|----------------|
## 14. Links to Detailed Documents
| Document | Purpose |
|----------|---------|
## 15. Evidence Mapping
| Claim | Source document/file | Evidence | Confidence |
|-------|----------------------|----------|------------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready “Architecture Overview” document.
Keep it high-level but factual.
Do not hallucinate.
Base every major claim on evidence.
Mark unknowns explicitly.

⸻

8.11 Prompt 11 — Hallucination Review

When to use: after every AI-generated architecture document.

You are acting as a strict Architecture Documentation Reviewer.
I will provide you with:
1. The generated architecture document.
2. The original .txt files and/or Evidence Base used to create it.
Your task is to review the document for hallucinations, unsupported claims, weak assumptions, and claims not grounded in the provided evidence.
================================================================================
CRITICAL REVIEW RULES
================================================================================
1. Do NOT add new architecture information.
2. Do NOT silently fix unsupported claims.
3. Identify every claim that is not directly supported by the provided files or Evidence Base.
4. Mark each problematic claim as one of:
   - Unsupported
   - Weakly inferred
   - Contradicted by evidence
   - Overstated certainty
   - Missing evidence
5. If a claim is only partially supported, explain what part is supported and what part is not.
6. If the document says something is confirmed but evidence only supports inference, downgrade it.
7. If a component, service, API, database, topic, dependency, flow, or infrastructure is not found in evidence, mark it clearly.
8. Use English language.
9. Be strict and factual.
10. Prefer removing unsupported claims over rewriting them as confident statements.
================================================================================
OUTPUT FORMAT
================================================================================
# Hallucination and Evidence Review
## 1. Review Summary
Provide:
- number of unsupported claims found
- number of weakly inferred claims
- number of contradicted claims
- overall document reliability
## 2. Unsupported or Weak Claims
| Claim | Problem type | Why it is problematic | Evidence status | Recommended correction |
|------|--------------|-----------------------|----------------|------------------------|
## 3. Claims That Should Be Downgraded
| Original claim | Current certainty | Correct certainty | Reason |
|---------------|------------------|-------------------|--------|
## 4. Claims That Should Be Removed
| Claim to remove | Reason | Evidence status |
|----------------|--------|----------------|
## 5. Corrected Version
Provide a corrected version of the document section by section.
Rules:
1. Do not add new unsupported claims.
2. Use only confirmed evidence.
3. Mark unknowns explicitly.
4. Replace speculative language with:
   “Unknown / Not confirmed from provided files”
   or
   “Inferred from naming/structure — not confirmed”
5. Preserve the original document structure where possible.
================================================================================
FINAL INSTRUCTION
================================================================================
Review the provided document strictly.
Identify hallucinations and unsupported claims.
Then produce a corrected evidence-based version.

⸻

8.12 Prompt 12 — Feature Impact Analysis

When to use: before implementing a new feature.

You are acting as a Principal Software Architect, Staff Backend Engineer, and Feature Design Reviewer.
I will provide you with:
1. A description of a new feature.
2. Existing architecture documents, such as Evidence Base, Service Inventory, API and Integration Architecture, Data Architecture, E2E Flows, Deployment, Security, and Resilience Review.
3. Optionally, related source code/configuration files.
Your task is to analyze the impact of the proposed feature on the existing architecture.
================================================================================
CRITICAL RULES
================================================================================
1. Use only provided architecture documents and files.
2. Do NOT invent existing services, APIs, databases, topics, or flows.
3. Clearly separate:
   - confirmed existing architecture
   - proposed feature changes
   - assumptions
   - open questions
4. Do NOT propose implementation until ownership, data changes, integration impact, transaction consistency, security, and observability are analyzed.
5. Every recommendation must include reasoning.
6. If current architecture does not provide enough information, write:
   “Unknown / requires verification”.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# Feature Impact Analysis: <feature-name>
## 1. Feature Summary
## 2. Existing Architecture Context
| Area | Current state | Evidence | Confidence |
|------|---------------|----------|------------|
## 3. Ownership Decision
| Candidate service | Why it may own the feature | Evidence | Risk | Recommendation |
|------------------|----------------------------|----------|------|----------------|
## 4. API Impact
| API | Change type | Compatibility risk | Recommendation |
|-----|-------------|--------------------|----------------|
## 5. Data Impact
| Entity/table | Change | Migration needed? | Ownership | Risk |
|-------------|--------|-------------------|-----------|------|
## 6. Messaging / Event Impact
| Event/topic | Producer | Consumer | Payload change | Reliability needs |
|------------|----------|----------|----------------|-------------------|
## 7. Updated E2E Flow
Create Mermaid sequence diagram for the proposed feature.
## 8. Transaction and Consistency Analysis
| Operation | Transaction boundary | Consistency risk | Mitigation |
|----------|----------------------|------------------|------------|
## 9. Resilience Requirements
| Concern | Required? | Reason | Recommendation |
|---------|-----------|--------|----------------|
Cover:
- retries
- DLQ
- idempotency
- outbox
- timeout
- duplicate handling
- fallback
## 10. Security Requirements
| Area | Requirement | Reason |
|------|-------------|--------|
## 11. Observability Requirements
| Area | What to add | Why |
|------|-------------|-----|
## 12. Deployment / Configuration Impact
| Config/deployment item | Change | Risk |
|------------------------|--------|------|
## 13. Risks and Open Questions
| Risk/question | Why it matters | Owner / place to verify |
|---------------|----------------|--------------------------|
## 14. Recommendation
Provide a clear implementation recommendation:
- recommended owning service
- recommended API changes
- recommended data changes
- recommended events
- required reliability/security/observability controls
- whether ADR is needed
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Feature Impact Analysis.
Do not hallucinate existing architecture.
Clearly separate existing facts from proposed changes.

⸻

8.13 Prompt 13 — Feature Architecture Delta

When to use: after impact analysis, before or during feature implementation.

You are acting as a Staff Backend Engineer and Software Architect.
I will provide you with:
1. A feature description.
2. Feature Impact Analysis.
3. Existing architecture documents.
4. Optionally, related code/configuration files.
Your task is to create a Confluence-ready document called:
“Feature Architecture Delta: <feature-name>”
This document must describe how the new feature changes the existing architecture.
================================================================================
CRITICAL RULES
================================================================================
1. Clearly separate existing architecture from proposed changes.
2. Do NOT invent existing services, APIs, DB tables, topics, or runtime components.
3. Proposed changes must be explicitly marked as proposed.
4. Every existing architecture statement must be evidence-based.
5. Every new proposed component/change must include rationale.
6. Document transaction, consistency, failure handling, security, observability, and rollback.
7. If something is unknown, mark it explicitly.
================================================================================
OUTPUT LANGUAGE
================================================================================
Write in English.
================================================================================
OUTPUT FORMAT
================================================================================
# Feature Architecture Delta: <feature-name>
## 1. Feature Goal
## 2. Existing Context
| Area | Current state | Evidence |
|------|---------------|----------|
## 3. Affected Services
| Service | Existing responsibility | Proposed change | Reason | Risk |
|--------|-------------------------|-----------------|--------|------|
## 4. API Changes
| API | Change type | Request changes | Response changes | Compatibility risk |
|-----|-------------|-----------------|------------------|--------------------|
## 5. Data Model Changes
| Entity/table | Change | Migration needed | Ownership | Risk |
|-------------|--------|------------------|-----------|------|
## 6. Messaging/Event Changes
| Event/topic | Producer | Consumer | Payload changes | Retry/DLQ/idempotency |
|------------|----------|----------|-----------------|-----------------------|
## 7. Updated E2E Flow
Create Mermaid sequence diagram.
## 8. Transaction and Consistency Design
| Operation | Transaction boundary | Consistency model | Failure risk | Mitigation |
|----------|----------------------|-------------------|--------------|------------|
## 9. Error Handling and Failure Modes
| Failure scenario | Expected behavior | Retry? | DLQ? | User-visible result |
|-----------------|-------------------|--------|------|---------------------|
## 10. Security Changes
| Endpoint/action | Auth required | Authorization rule | Sensitive data risk |
|----------------|---------------|--------------------|--------------------|
## 11. Observability Changes
| Area | What to add | Why |
|------|-------------|-----|
## 12. Deployment/Configuration Changes
| Config/deployment item | Change | Environment | Risk |
|------------------------|--------|-------------|------|
## 13. Risks and Mitigations
| Risk | Impact | Mitigation | Owner |
|------|--------|------------|-------|
## 14. Rollback Plan
Describe:
- how to disable the feature
- whether feature flag exists
- whether migration is reversible
- what happens to produced events
- how to handle partially processed data
## 15. Open Questions
| Question | Why it matters | Owner |
|----------|----------------|-------|
================================================================================
FINAL INSTRUCTION
================================================================================
Generate the complete Confluence-ready Feature Architecture Delta.
Keep existing facts evidence-based.
Clearly mark proposed changes.

⸻

9. How to Use the Prompt Pack for Existing Architecture Analysis

9.1 Step-by-Step Process

Step	Prompt	Output
1	Evidence Base	Factual source-of-truth
2	Service Inventory	Services, ownership, responsibilities
3	Hallucination Review	Clean service document
4	API and Integration	REST/messaging/external integrations
5	Hallucination Review	Clean integration document
6	Data Architecture	DB/entities/transactions
7	Hallucination Review	Clean data document
8	E2E Business Flows	Flow diagrams
9	Hallucination Review	Clean flow document
10	Deployment Architecture	Runtime/deployment
11	Security Architecture	Auth/authz/secrets
12	Resilience Review	Production readiness
13	Risks and Recommendations	Consolidated risks
14	Architecture Overview	High-level summary

9.2 Workflow Diagram

flowchart TD
    A[Collect source files] --> B[Prompt 1: Evidence Base]
    B --> C[Prompt 2: Service Inventory]
    C --> D[Prompt 11: Hallucination Review]
    D --> E[Prompt 3: API and Integration]
    E --> F[Prompt 4: Data Architecture]
    F --> G[Prompt 5: E2E Flows]
    G --> H[Prompt 6: Deployment]
    H --> I[Prompt 7: Security]
    I --> J[Prompt 8: Resilience and Observability]
    J --> K[Prompt 9: Risks and Recommendations]
    K --> L[Prompt 10: Architecture Overview]
    L --> M[Publish to Confluence]

⸻

10. How to Use the Prompt Pack for New Feature Development

10.1 Before Coding

Use the existing architecture documents and the feature description.

Run:

Prompt 12 — Feature Impact Analysis

This should answer:

Question	Why It Matters
Which service owns this feature?	Prevents wrong service ownership
What APIs change?	Prevents client breakage
What data changes?	Prevents DB ownership violations
What events change?	Prevents async integration issues
What E2E flow changes?	Makes full impact visible
What transaction boundaries exist?	Prevents consistency bugs
Is idempotency needed?	Handles retries and duplicates
Is DLQ needed?	Handles poison messages
What security rules apply?	Prevents endpoint exposure
What observability is needed?	Supports production operations

⸻

10.2 During Design

Create:

Prompt 13 — Feature Architecture Delta

The output becomes the feature design document.

It can be attached to:

* Jira ticket;
* Epic;
* Confluence feature page;
* PR description;
* ADR if needed.

⸻

10.3 During Implementation

Use the architecture documents as guardrails.

Area	Guardrail
Service ownership	Do not implement the feature in the wrong service
API	Preserve backward compatibility
Data	Do not modify data owned by another service
Transactions	Keep transaction boundary explicit
Messaging	Add idempotency/retry/DLQ where needed
Security	Protect endpoints and sensitive data
Observability	Add logs, metrics, and traces
Deployment	Update config, env variables, and secrets
Tests	Cover E2E and failure modes

⸻

10.4 During Code Review

Use AI with a code review prompt or extend the Hallucination Review style.

Review for:

Area	Questions
Service boundary	Is logic placed in the right service?
API contract	Is compatibility preserved?
Data ownership	Is DB ownership respected?
Transaction consistency	Are DB writes and events safe?
Idempotency	Are retries and duplicates handled?
Error handling	Are failure modes explicit?
Security	Are endpoints protected?
Observability	Are logs, metrics, and traces sufficient?
Deployment	Are configs documented?
Documentation	Are Confluence docs updated?

⸻

11. Example: New Feature Flow

11.1 Example Feature

Add document upload with asynchronous virus scanning and notification.

This is an illustrative example only.

⸻

11.2 Feature Impact Analysis

Expected questions:

Area	Question
Ownership	Does Document Service own the upload lifecycle?
API	Do we need POST /documents?
Data	Do we need Document and ScanResult entities?
Messaging	Do we need a DocumentUploaded event?
Consistency	How do we handle DB write + event publish?
Resilience	Do we need retry/DLQ for the scanner?
Security	Who can upload/read the document?
Observability	What metrics/logs/traces are needed?

⸻

11.3 Proposed E2E Flow

sequenceDiagram
    participant Client
    participant DocumentService
    participant Storage
    participant DB
    participant Kafka
    participant ScannerService
    participant NotificationService
    Client->>DocumentService: POST /documents
    DocumentService->>DocumentService: Validate metadata and file constraints
    DocumentService->>Storage: Store file
    DocumentService->>DB: Persist document metadata
    DocumentService->>Kafka: Publish DocumentUploaded event
    Kafka->>ScannerService: Consume DocumentUploaded
    ScannerService->>Storage: Read file
    ScannerService->>ScannerService: Scan file
    ScannerService->>DB: Update scan status
    ScannerService->>Kafka: Publish DocumentScanned event
    Kafka->>NotificationService: Consume DocumentScanned
    NotificationService->>Client: Send notification

In a real document, every participant and arrow must be backed by evidence or explicitly marked as proposed.

⸻

11.4 Key Design Controls

Concern	Recommended Control
Duplicate upload	Idempotency key
DB write + event publish	Transactional outbox
Scanner failure	Retry with backoff
Poison scan event	DLQ
External scanner timeout	Timeout
Unauthorized document access	Ownership-based authorization
Missing observability	Correlation ID + metrics + traces
Rollback	Feature flag + migration rollback plan

⸻

12. Definition of Done for AI-Assisted Architecture Work

Requirement	Done?
Evidence Base created	
Evidence Base manually reviewed	
Focused documents generated	
Hallucination Review completed	
Unsupported claims removed/downgraded	
Diagrams validated against evidence	
Unknowns documented	
Risks prioritized	
Architecture Overview generated last	
Confluence pages published	
Owners assigned	
Feature Delta created for new feature	
Docs updated after implementation	

⸻

13. Final Operating Rules

1. Never trust AI-generated architecture documentation without evidence review.
2. Always start with Evidence Base.
3. Never generate Architecture Overview first.
4. Split prompts by architecture concern.
5. Use Hallucination Review after every generated document.
6. Every diagram arrow must have evidence.
7. Unknowns are valuable and must stay visible.
8. For every non-trivial feature, create Feature Impact Analysis.
9. For every significant feature, create Feature Architecture Delta.
10. Update Confluence as part of Definition of Done.

⸻

14. Final Recommendation

This approach should be used as a standard process for:

* analyzing existing architecture;
* documenting v1/v2 systems;
* describing E2E flows;
* planning new features;
* conducting architecture reviews;
* supporting code reviews;
* onboarding engineers;
* reviewing production readiness.

The main principle:

AI should not invent architecture. AI should help reconstruct architecture from evidence.

Correct use of AI in this context is not “ask and trust.” It is an engineering process:

source files → evidence base → focused docs → hallucination review → risks → overview → feature delta → implementation → docs update

That is how AI becomes a useful tool for backend/platform engineering instead of a source of polished but unreliable documentation.