Title: Implement evidence-based cross-repository architecture analysis

What?

As a software engineer or architect, I want to analyze all accessible GitHub repositories owned by a specified user or organization whose names start with an exact prefix, so that I can generate a consolidated, evidence-backed view of dependencies and runtime relationships across those repositories.

The solution shall be implemented and tested in IntelliJ IDEA with GitHub Copilot assistance. It shall provide project-local commands for:

* Environment bootstrap and prerequisite validation
* Repository discovery and synchronization
* Per-repository code graph generation
* Cross-repository graph merging
* Relationship evidence collection and validation
* Architecture report generation
* Incremental updates
* Graph querying

The implementation shall use the existing Python, Bash, JSON Schema, GitHub CLI, and Graphify architecture. It is not an IntelliJ plugin, and Copilot must not become a runtime dependency.

Why?

Related repositories often contain hidden architectural coupling through REST or gRPC calls, Kafka topics, shared contracts, databases, deployment configuration, and build dependencies.

Manual analysis is slow, difficult to reproduce, and prone to assumptions. The proposed capability will provide:

* Repeatable discovery and analysis
* Traceable architecture relationships
* Source-level evidence for every reported relationship
* A consolidated cross-repository architecture view
* Safe analysis of public and private repositories
* Locally stored reports without publishing sensitive architecture information

Acceptance Criteria

AC1 — Bootstrap and prerequisite validation

Given the project is opened from its root directory,
when the bootstrap command is executed,
then the system verifies the availability of `git`, `gh`, authenticated GitHub access, `uv`, Python 3.9+, `jq`, and Graphify,
and creates all required git-ignored runtime directories.

AC2 — Input validation

Given an analysis request,
when `owner` and `prefix` are provided,
then both values are validated against an explicitly defined safe character set,
and invalid or missing values cause the operation to fail before executing GitHub, shell, or filesystem operations.

AC3 — Repository discovery

Given a valid GitHub owner or organization and an exact prefix,
when repository discovery runs,
then all accessible repositories are retrieved using pagination,
and only repository names beginning with the exact, case-sensitive prefix are selected.

Archived repositories and forks are excluded by default but recorded with an explicit skipped status.

AC4 — Safe repository synchronization

Given the discovered repository inventory,
when synchronization runs,
then each selected repository is cloned or safely fast-forward updated inside the git-ignored workspace.

For every repository, the system records:

* Repository identity and origin
* Default branch
* Exact analyzed commit SHA
* Synchronization status
* Failure details, if applicable

The implementation must not use force checkout, hard reset, force push, destructive cleanup, or automatic modification of a dirty repository.

AC5 — Clone completeness gate

Given repository synchronization has finished,
when the completeness gate runs,
then analysis continues only if every selected repository has:

* Exactly one valid local clone
* The expected Git origin
* A resolvable HEAD
* An exact recorded commit SHA
* No uncommitted changes
* No authentication or synchronization failure

If any validation fails, the pipeline stops and reports each affected repository and reason.

AC6 — Code graph generation

Given all repositories pass the completeness gate,
when graph generation runs,
then Graphify performs local code-only extraction for every repository using bounded concurrency.

Each generated graph must be validated before all valid repository graphs are merged into one cross-repository graph.

AC7 — Relationship evidence collection

Given valid repository graphs and synchronized source code,
when evidence collection runs,
then the system identifies supported relationships, including:

* Build dependencies
* HTTP or REST calls
* gRPC or protobuf dependencies
* Kafka producers and consumers
* Shared OpenAPI, AsyncAPI, or Avro contracts
* Shared database access
* Flyway or Liquibase ownership
* Docker, Kubernetes, Helm, or Terraform coupling
* CI/CD and deployment dependencies
* Service discovery references

Every reported relationship includes at least:

* Source repository
* Target repository
* Relationship type
* Analyzed commit SHA
* File path
* Relevant line range
* Confidence level
* Human-readable explanation

AC8 — Confidence classification

Given a detected relationship,
when it is evaluated,
then it is classified as one of:

* `CONFIRMED`
* `PROBABLE`
* `POSSIBLE`
* `CONTRADICTORY`
* `UNRESOLVED`

Matching names or an inferred Graphify edge alone must not produce a confidence higher than `POSSIBLE`. Relationships requiring evidence from both sides must not be promoted without that evidence.

AC9 — Schema validation

Given generated inventories, summaries, manifests, graphs, or relationships,
when validation runs,
then every artifact is checked against its corresponding JSON Schema and confidence policy.

Invalid artifacts cause the pipeline to fail or be explicitly downgraded according to the configured validation mode. Invalid data must not silently appear in the final report.

AC10 — Report generation

Given validated analysis artifacts,
when report generation runs,
then the system produces local Markdown reports containing:

* Repository inventory and status
* Cross-repository relationships
* Relationship evidence and confidence
* Unresolved or contradictory findings
* Analysis metadata and commit SHAs

A successfully completed run is recorded as `SUCCEEDED`. An unfinished two-phase run remains explicitly marked as `PARTIAL`.

AC11 — Incremental update

Given a previously successful analysis,
when the update command is executed,
then the system reuses the recorded owner and prefix, synchronizes changed repositories, avoids unnecessary processing where safely possible, and regenerates affected artifacts.

If no previous successful configuration exists, the command fails with an actionable message.

AC12 — Validation and query commands

Given existing artifacts:

* The validation command revalidates them without rediscovering or recloning repositories.
* The query command uses the validated merged graph.
* If no valid merged graph exists, the query command fails clearly and recommends running analysis first.
* The report command regenerates reports only from validated data.

AC13 — Security and privacy

The implementation:

* Performs no GitHub write operations during analysis
* Executes no destructive Git commands
* Does not commit or publish cloned repositories or generated analysis data
* Keeps repositories, graphs, reports, logs, temporary files, and runtime data git-ignored
* Never exposes credentials, source code, or architecture evidence in logs
* Uses configurable bounds for clone, Graphify, and analysis concurrency

AC14 — Automated testing

The solution includes:

* Offline unit tests for deterministic Python logic
* Shell smoke tests for the complete supported workflow
* Synthetic provider, consumer, and shared-contract repositories
* Tests for invalid input, pagination, dirty clones, origin mismatch, malformed graphs, schema violations, missing evidence, and interrupted runs
* CI execution that requires no access to real private repositories or external AI services

AC15 — Definition of Done

The story is complete when:

* All acceptance criteria are implemented
* Automated tests pass locally and in CI
* Static analysis and formatting checks pass
* The README documents setup, commands, security constraints, troubleshooting, and example usage
* A synthetic end-to-end analysis produces a validated merged graph and evidence-backed reports
* No generated runtime output is committed
* The implementation is reviewed and merged through a pull request
