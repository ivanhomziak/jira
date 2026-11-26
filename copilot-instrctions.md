## Spec-Driven Development Workflow

This repository uses GitHub Spec Kit and Graphify.

### Sources of truth

- Jira Markdown files: approved business requirements.
- Source code and tests: current system behavior.
- Graphify: architectural context and dependency discovery.
- Spec Kit constitution: engineering constraints.
- Spec Kit specifications: approved feature requirements.
- Spec Kit plans: approved technical decisions.
- Spec Kit tasks: implementation scope and progress.

### Graphify integration

Before planning or implementing changes:

- Use scoped Graphify queries to discover relevant components.
- Identify affected modules and dependencies.
- Verify graph findings against source code and tests.
- Do not treat inferred graph relationships as verified facts.
- Avoid loading the entire graph into the context.
- Refresh the graph when relevant source changes make it stale.

### Feature development

For each Jira user story:

1. Read the original Jira Markdown file.
2. Generate the feature specification using Spec Kit.
3. Clarify ambiguous requirements.
4. Analyze the existing architecture using Graphify.
5. Generate the technical plan.
6. Generate implementation tasks and test requirements.
7. Validate consistency across all artifacts.
8. Request human approval before modifying application code.
9. Implement only approved tasks.
10. Execute relevant tests.
11. Refresh Graphify.
12. Perform a production-readiness review.

### Constraints

- Preserve original Jira acceptance criteria.
- Do not invent business requirements.
- Preserve backward compatibility.
- Reuse existing architectural patterns.
- Avoid unrelated refactoring.
- Do not modify original Jira Markdown files.
- Report deviations and unresolved risks.
