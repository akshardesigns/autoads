# BriefStudio Engineering Roadmap

## Roadmap Principles

- Preserve existing UI, user workflow, and features unless change is explicitly approved.
- Establish tests and contracts before moving implementation.
- Keep the application deployable at the end of every Sprint.
- Separate structural refactor, visual redesign, schema migration, and new features.
- Prioritize maintainability, scalability, readability, and reusability.

Sprint boundaries describe engineering outcomes, not fixed calendar duration.

## Sprint 1 — Engineering Foundation

### Objective

Create a shared engineering baseline before refactoring application code.

### Deliverables

- Professional project README
- `AGENTS.md` Engineering Constitution
- Current and target architecture documentation
- Draft unified JSON specification
- Six-Sprint engineering roadmap
- Initial changelog
- ADR covering Vanilla technology and incremental refactor strategy
- Verified zero changes to application implementation

### Risiko

- Documentation may diverge from implementation if ownership is unclear.
- Target architecture may be mistaken for already-delivered functionality.
- Cover Studio terminology may be confused with existing Story/Reels mode.

Mitigation: clearly label current versus target state, record product ambiguities, and update documentation in future PRs.

### Exit Criteria

- All foundation documents are reviewed and approved.
- Current architecture and technical constraints are accurately represented.
- Seven target Studios are named consistently.
- JSON compatibility principles are documented.
- `index.html` is unchanged.
- Sprint 2 scope is approved before implementation begins.

## Sprint 2 — Foundation Refactor

### Objective

Introduce safe tooling and module boundaries without changing UI, workflow, or generated legacy JSON.

### Deliverables

- Behavior and JSON baseline fixtures for all current modes
- Unit test runner and browser-level smoke tests
- Formatting and linting configuration
- Minimal local development and production build workflow
- JavaScript and CSS extracted from `index.html` into cacheable modules/assets
- Application bootstrap and Studio registry boundary
- Compatibility test proving legacy outputs remain stable

### Risiko

- Script load order or DOM timing regressions
- Accidental CSS cascade differences after extraction
- Snapshot tests may preserve existing bugs without clearly labelling them
- Tooling may add complexity disproportionate to the project

Mitigation: migrate one boundary at a time, compare visual and JSON output, keep dependencies minimal, and maintain known-defect tests separately.

### Exit Criteria

- Critical workflows pass automated smoke tests.
- Existing UI is visually equivalent at agreed viewport baselines.
- Existing legacy JSON fixtures remain compatible.
- No inline implementation is moved without coverage.
- Development and build commands are documented and reproducible.

## Sprint 3 — Shared Component System

### Objective

Remove repeated interaction and markup patterns while preserving product behavior.

### Deliverables

- Shared field, toggle, tags, color, ratio, tabs, and action components
- Centralized event handling without inline handlers for migrated areas
- Explicit application and Studio state model
- Accessible form labeling and validation presentation
- Accessible dialog, status, drawer, and toast behavior
- Reusable repeatable-item editor foundation for Carousel and Grid

### Risiko

- Subtle keyboard, focus, or state persistence regressions
- Over-generalized components that make Studio-specific behavior harder
- Hidden coupling to existing class names

Mitigation: extract only demonstrated repetition, retain component escape hatches, and test keyboard plus pointer workflows.

### Exit Criteria

- Shared patterns are implemented once for migrated Studios.
- No duplicate source of truth exists for migrated state.
- Accessibility checks pass the agreed automated and manual baseline.
- UI and workflow changes outside approved accessibility corrections are absent.
- Component contracts are documented.

## Sprint 4 — Unified JSON Engine

### Objective

Implement a versioned canonical JSON contract with validation, compatibility, and exporter boundaries.

### Deliverables

- Machine-readable canonical schema
- Runtime validator with structured issues
- Shared brief builder and normalized summary projection
- Legacy-to-canonical and canonical-to-legacy adapters
- Capability-aware JSON, ChatGPT, and Midjourney exporters
- Schema fixtures for every Studio
- Versioning and migration test suite

### Risiko

- Breaking undocumented downstream consumers
- Loss of Studio-specific expressiveness
- Incorrect conversion of localized UI labels to machine values
- Dual-output migration complexity

Mitigation: preserve raw fixtures, provide dual output during transition, use explicit mappings, and require product review for field semantics.

### Exit Criteria

- Every implemented Studio produces schema-valid canonical JSON.
- Legacy compatibility tests pass.
- Exporters do not inspect arbitrary Studio-specific paths.
- Unsupported export capabilities fail explicitly.
- No runtime error occurs for copy, summary, or download across Studios.

## Sprint 5 — Studio Migration

### Objective

Move all seven target Studios onto the shared component and JSON architecture.

### Deliverables

- Feed Studio migration as reference implementation
- Carousel Studio migration with controlled repeatable state
- Typography Ads Studio migration
- Product decision and implementation contract for Cover Studio
- Thumbnail Studio migration
- Grid Studio migration with deterministic nine-tile model
- Product Showcase Studio migration
- Studio-specific validation, fixtures, and documentation

### Risiko

- Uneven maturity between Studios
- Product scope expansion during technical migration
- Legacy Story/Reels and Cover naming conflict
- Cross-Studio regression from shared component changes

Mitigation: migrate one Studio per reviewable change, freeze scope per Studio, and require compatibility plus E2E checks before proceeding.

### Exit Criteria

- Seven target Studios are registered and documented.
- Each Studio owns only format-specific behavior.
- Shared concepts use canonical components and fields.
- Every Studio has valid, invalid, and compatibility fixtures.
- Legacy behavior is retained or explicitly migrated with approval.

## Sprint 6 — Production Hardening

### Objective

Prepare BriefStudio for reliable public release and ongoing development.

### Deliverables

- CI checks for formatting, linting, tests, schema compatibility, and build
- Cross-browser and responsive QA matrix
- Accessibility audit and remediation to agreed WCAG target
- Security review, URL validation, and Content Security Policy plan
- Performance budget and optimized font/static asset delivery
- Error handling and observability strategy
- Release, deployment, rollback, and contribution documentation
- Explicit project license

### Risiko

- Browser-specific Clipboard, download, and viewport behavior
- Late accessibility fixes impacting component APIs
- Deployment assumptions not represented in local development
- Expanding release criteria delaying delivery

Mitigation: define measurable budgets early, automate repeatable checks, and separate launch blockers from post-launch improvements.

### Exit Criteria

- CI blocks regressions on the default branch.
- Critical workflows pass supported-browser tests.
- Accessibility and performance targets are met and recorded.
- Security findings have resolutions or accepted risk records.
- Deployment and rollback are rehearsed.
- License and release documentation are complete.

## Approval Gates

Progression between Sprints requires:

1. Exit criteria evidence
2. Updated changelog and documentation
3. Known risks and deferred items
4. Maintainer approval of the next Sprint scope

