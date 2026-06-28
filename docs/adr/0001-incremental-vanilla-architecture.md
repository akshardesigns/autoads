# ADR-0001: Retain Vanilla Web Technology and Refactor Incrementally

- Status: Accepted for Sprint 1 planning
- Date: 2026-06-28
- Decision owners: BriefStudio maintainers
- Scope: Foundation refactor strategy

## Context

BriefStudio is a functional static browser application implemented in one `index.html` file. The application has no backend and its runtime needs are modest: form interaction, small in-memory collections, JSON construction, DOM rendering, clipboard operations, and local downloads.

The engineering audit identified significant maintainability and compatibility debt, but it did not identify a product requirement that inherently requires a frontend framework or a typed build pipeline. A full rewrite would combine architecture migration, behavior recreation, schema redesign, and potential UI changes in one high-risk effort.

## Decision

BriefStudio will retain Vanilla HTML, CSS, and JavaScript during the foundation refactor. The project will introduce module boundaries, tests, reusable components, and a unified JSON contract incrementally.

React and TypeScript will not be introduced in the initial refactor. They may be reconsidered through separate ADRs when evidence demonstrates that their benefits exceed migration and maintenance costs.

## Why Keep Vanilla HTML/CSS/JavaScript

- Current runtime requirements are well supported by the web platform.
- The application deploys as static files with no runtime framework dependency.
- Existing contributors can inspect behavior without framework-specific abstractions.
- Small modules and explicit state ownership solve the immediate architecture problems.
- Keeping the runtime stable reduces the number of variables during extraction.
- It preserves the current loading and hosting model.

This decision does not mean retaining the single-file architecture. Vanilla code must still be modular, testable, accessible, and organized around clear boundaries.

## Why Not React Yet

React could provide component composition, declarative rendering, and established state patterns. It is not selected now because:

- The present problem is coupling, not an inability to render the UI.
- A React migration would require recreating all existing controls and interactions at once or maintaining two rendering systems.
- It could hide schema and state design problems behind framework components rather than resolving them.
- It adds runtime, build, dependency, and contributor maintenance costs.
- No current requirement for routing, collaborative state, server rendering, or large-scale reactive views justifies the migration.

React should be reconsidered if future requirements include complex live previews, extensive routing, collaborative editing, or state interactions that become costly to maintain with lightweight modules.

## Why Not TypeScript Yet

TypeScript would be valuable for canonical schemas, Studio contracts, and exporter capabilities. It is deferred initially because:

- The code has not yet been separated into stable module boundaries.
- Adding types while moving and redesigning contracts would increase the size of the first refactor.
- Premature types could encode current inconsistent schemas as permanent abstractions.
- JavaScript plus runtime validation and tests can establish correct boundaries first.

TypeScript should be reconsidered after the canonical JSON contract and module responsibilities stabilize. Adoption can then proceed module by module with clearer value and lower churn.

## Why Incremental Refactor Instead of Rewrite

- Existing behavior is valuable even where implementation quality is weak.
- A rewrite risks silently removing workflows and output fields.
- Compatibility can be measured only when old and new paths coexist long enough to compare.
- Small changes are easier to review, test, roll back, and release.
- The team can establish tests before changing behavior.
- Product decisions, such as Cover versus Story/Reels, can be resolved independently of code movement.

The sequence is:

1. Document current behavior and target contracts.
2. Capture fixtures and critical workflow tests.
3. Extract assets and module boundaries without behavioral change.
4. Introduce shared components and explicit state.
5. Add the canonical JSON engine with adapters.
6. Migrate one Studio at a time.

## Consequences

### Positive

- Lower regression and rollback risk
- Static deployment remains simple
- Existing user workflow is protected
- Architecture decisions can be validated before broader adoption
- Framework and language choices remain evidence-driven

### Negative

- Some duplication and legacy patterns will coexist during migration.
- Temporary adapters increase short-term code volume.
- Vanilla components require discipline around lifecycle and event ownership.
- Type safety remains limited until a later decision.

### Risks

- “Temporary” legacy paths may remain longer than planned.
- Contributors may interpret Vanilla as permission to add more globals or inline handlers.
- Parallel refactors can cross module boundaries before contracts stabilize.

Mitigation: enforce `AGENTS.md`, track migrations in the roadmap, require compatibility tests, and remove deprecated paths through explicit milestones.

## Reconsideration Triggers

Create a new ADR if one or more conditions occur:

- UI state complexity materially exceeds the shared state model.
- Complex real-time previews become a core requirement.
- Multiple routes or collaborative editing are introduced.
- Canonical schemas stabilize and TypeScript would prevent demonstrated defect classes.
- Framework ecosystem capabilities provide a measured benefit that outweighs migration cost.

## Alternatives Considered

### Immediate React rewrite

Rejected for now because it combines too many risks and does not directly solve JSON compatibility.

### Immediate TypeScript conversion

Deferred because current module and schema boundaries are not yet stable.

### Preserve the single-file application indefinitely

Rejected because it cannot support the target number and depth of Studios maintainably.

### Adopt another framework immediately

Rejected for the same reasons as React: no present requirement justifies framework migration during foundation work.
