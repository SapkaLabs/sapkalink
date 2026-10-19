# Issue tracker: GitHub

The canonical tracker is the public GitHub Issues tracker for `SapkaLabs/sapkalink`. Use the GitHub CLI/API with this explicit repository; never infer a different repository from the surrounding workspace.

## Wayfinding operations

- Map: one issue labeled `wayfinder:map`, containing Destination, Notes, Decisions so far, Not yet specified, and Out of scope. Its body does not enumerate open tickets; native sub-issues expose them.
- Tickets: native child issues, labeled only `wayfinder:research`, `wayfinder:task`, `wayfinder:grilling`, or `wayfinder:prototype` as appropriate. They contain a decision/investigation question, not an implementation assignment.
- Claim: assign the ticket to the driving developer before working it. Open, unassigned children are unclaimed.
- Blocking: native GitHub issue dependencies, created after real child issue IDs exist. `issue_dependencies_summary.blocked_by` describes open blockers. Use a body fallback only if the tracker demonstrably lacks native relationships.
- Frontier: open child issues with no open blockers and no assignee. Refer to issues by linked titles in human-facing text.
- Resolve: publish the evidence-backed answer as a resolution comment, close the resolved ticket, then add its linked-title gist to the map's Decisions so far. Do not close an unresolved investigation to make the map look complete.
- Research: follow primary sources, retain exact revision/source boundaries, publish only original sanitized notes on a disposable `research/<topic>` branch, link it from the ticket, open no PR, and never merge automatically.

Research may run in parallel. Human decision tickets require the actual owner; agents must not invent the owner's answers. Existing user instructions and confirmed requirements take precedence over unnecessary re-approval of routine planning choices.

PRs as a triage request surface: no. Application implementation is not authorized by a Wayfinder research ticket.
