# Implement Evidence-Based Cross-Repository Architecture Analysis

## User Story

As a software engineer or architect, I want to analyze all accessible GitHub repositories owned by a specified user or organization whose names start with an exact prefix, so that I can generate a consolidated, evidence-backed view of dependencies and runtime relationships across those repositories.

The solution shall be implemented and tested in IntelliJ IDEA with GitHub Copilot assistance. It shall provide project-local commands for:

- Environment bootstrap and prerequisite validation
- Repository discovery and synchronization
- Per-repository code graph generation
- Cross-repository graph merging
- Relationship evidence collection and validation
- Architecture report generation
- Incremental updates
- Graph querying

The implementation shall use the existing Python, Bash, JSON Schema, GitHub CLI, and Graphify architecture. It is not an IntelliJ plugin, and GitHub Copilot must not become a runtime dependency.

## What?

Implement a reusable, project-scoped capability that:

1. Discovers repositories for a configured GitHub owner or organization using an exact, case-sensitive repository-name prefix.
2. Clones or safely updates the selected repositories in an isolated, git-ignored workspace.
3. Generates and merges per-repository Graphify code graphs.
4. Detects build-time, runtime, data, contract, deployment, and operational relationships.
5. Produces validated architecture reports where every relationship is backed by source-level evidence.
6. Supports repeatable full analysis, incremental updates, artifact validation, graph queries, and report regeneration.

Reference repository: <https://github.com/IvanHomziak/repository-architecture-hub>

## Why?

Related repositories frequently contain hidden architectural coupling through REST or gRPC calls, Kafka topics, shared contracts, databases, deployment configuration, and build dependencies.

Manual analysis is slow, difficult to reproduce, and prone to unsupported assumptions. This capability will provide:

- Repeatable repository discovery and architecture analysis
- Traceable cross-repository relationships
- Source-level evidence for every reported relationship
- A consolidated cross-repository architecture view
- Safe analysis of public and private repositories
- Locally stored reports without publishing sensitive architecture information

## Acceptance Criteria

### AC1 — Bootstrap and prerequisite validation

**Given** the project is opened from its root directory,  
**When** the bootstrap command is executed,  
**Then** the system verifies the availability of `git`, `gh`, authenticated GitHub access, `uv`, Python 3.9+, `jq`, and Graphify,  
**And** creates all required git-ignored runtime directories.

### AC2 — Input validation

**Given** an analysis request,  
**When** `owner` and `prefix` are provided,  
**Then** both values are validated against an explicitly defined safe character set,  
**And** invalid or missing values cause the operation to fail before executing GitHub, shell, or filesystem operations.

### AC3 — Repository discovery

**Given** a valid GitHub owner or organization and an exact prefix,  
**When** repository discovery runs,  
**Then** all accessible repositories are retrieved using pagination,  
**And** only repository names beginning with the exact, case-sensitive prefix are selected,  
**And** archived repositories and forks are excluded by default but recorded with an explicit skipped status,  
**And** pagination truncation is detected and never silently accepted.

### AC4 — Safe repository synchronization

**Given** the discovered repository inventory,  
**When** synchronization runs,  
**Then** each selected repository is cloned or safely fast-forward updated inside the git-ignored workspace,  
**And** the following values are recorded for every repository:

- Repository identity and origin
- Default branch
- Exact analyzed commit SHA
- Synchronization status
- Failure details, if applicable

The implementation must not use force checkout, hard reset, force push, destructive cleanup, or automatic modification of a dirty repository.

### AC5 — Clone completeness gate

**Given** repository synchronization has finished,  
**When** the completeness gate runs,  
**Then** analysis continues only if every selected repository has:

- Exactly one valid local clone
- The expected Git origin
- A resolvable `HEAD`
- An exact recorded commit SHA
- No uncommitted changes
- No authentication or synchronization failure

**And** if any validation fails, the pipeline stops and reports each affected repository and reason.

### AC6 — Code graph generation

**Given** all repositories pass the completeness gate,  
**When** graph generation runs,  
**Then** Graphify performs local code-only extraction for every repository using bounded concurrency,  
**And** each generated graph is validated,  
**And** all valid repository graphs are merged into one cross-repository graph.

### AC7 — Relationship evidence collection

**Given** valid repository graphs and synchronized source code,  
**When** evidence collection runs,  
**Then** the system identifies supported relationships, including:

- Build dependencies
- HTTP or REST calls
- gRPC or protobuf dependencies
- Kafka producers and consumers
- Shared OpenAPI, AsyncAPI, or Avro contracts
- Shared database access
- Flyway or Liquibase ownership
- Docker, Kubernetes, Helm, or Terraform coupling
- CI/CD and deployment dependencies
- Service discovery references

**And** every reported relationship includes at least:

- Source repository
- Target repository
- Relationship type
- Analyzed commit SHA
- File path
- Relevant line range
- Confidence level
- Human-readable explanation

### AC8 — Confidence classification

**Given** a detected relationship,  
**When** it is evaluated,  
**Then** it is classified as one of:

- `CONFIRMED`
- `PROBABLE`
- `POSSIBLE`
- `CONTRADICTORY`
- `UNRESOLVED`

Matching names or an inferred Graphify edge alone must not produce a confidence higher than `POSSIBLE`. Relationships requiring evidence from both sides must not be promoted without that evidence.

### AC9 — Schema validation

**Given** generated inventories, summaries, manifests, graphs, or relationships,  
**When** validation runs,  
**Then** every artifact is checked against its corresponding JSON Schema and confidence policy,  
**And** invalid artifacts cause the pipeline to fail or are explicitly downgraded according to the configured validation mode,  
**And** invalid data does not silently appear in the final report.

### AC10 — Report generation

**Given** validated analysis artifacts,  
**When** report generation runs,  
**Then** the system produces local Markdown reports containing:

- Repository inventory and status
- Cross-repository relationships
- Relationship evidence and confidence
- Unresolved or contradictory findings
- Analysis metadata and commit SHAs

**And** a successfully completed run is recorded as `SUCCEEDED`,  
**And** an unfinished two-phase run remains explicitly marked as `PARTIAL`.

### AC11 — Incremental update

**Given** a previously successful analysis,  
**When** the update command is executed,  
**Then** the system reuses the recorded owner and prefix,  
**And** synchronizes changed repositories,  
**And** avoids unnecessary processing where safely possible,  
**And** regenerates affected artifacts.

If no previous successful configuration exists, the command fails with an actionable message.

### AC12 — Validation, query, and report commands

**Given** existing analysis artifacts:

- The validation command revalidates them without rediscovering or recloning repositories.
- The query command uses the validated merged graph.
- If no valid merged graph exists, the query command fails clearly and recommends running analysis first.
- The report command regenerates reports only from validated data.

### AC13 — Security and privacy

The implementation:

- Performs no GitHub write operations during analysis
- Executes no destructive Git commands
- Does not commit or publish cloned repositories or generated analysis data
- Keeps repositories, graphs, reports, logs, temporary files, and runtime data git-ignored
- Never exposes credentials, source code, or architecture evidence in logs
- Uses configurable bounds for clone, Graphify, and analysis concurrency

### AC14 — Automated testing

The solution includes:

- Offline unit tests for deterministic Python logic
- Shell smoke tests for the complete supported workflow
- Synthetic provider, consumer, and shared-contract repositories
- Tests for invalid input, pagination, dirty clones, origin mismatch, malformed graphs, schema violations, missing evidence, and interrupted runs
- CI execution that requires no access to real private repositories or external AI services

### AC15 — Definition of Done

The story is complete when:

- All acceptance criteria are implemented
- Automated tests pass locally and in CI
- Static analysis and formatting checks pass
- The README documents setup, commands, security constraints, troubleshooting, and example usage
- A synthetic end-to-end analysis produces a validated merged graph and evidence-backed reports
- No generated runtime output is committed
- The implementation is reviewed and merged through a pull request

## Implementation Guidance for IDEA and Copilot

- Open the repository root in IntelliJ IDEA.
- Use GitHub Copilot to implement one acceptance-criteria group at a time.
- Preserve deterministic Python and Bash boundaries; do not move judgment-sensitive logic into shell scripts.
- Keep generated runtime data outside version control.
- Require tests and validation before accepting Copilot-generated changes.
- Do not give Copilot unrestricted GitHub write or destructive filesystem permissions.

## Recommended Jira Breakdown

This scope is Epic-sized. For sprint execution, split it into the following child stories:

1. Bootstrap, input validation, and repository discovery
2. Safe repository synchronization and completeness gate
3. Graphify extraction and merged graph generation
4. Relationship evidence collection and confidence classification
5. Schema validation and architecture reports
6. Incremental update, query, and report commands
7. Security hardening, offline tests, and CI validation
