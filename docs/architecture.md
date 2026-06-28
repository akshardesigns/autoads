# BriefStudio Architecture

## Document Status

- Status: Initial architecture baseline
- Scope: Current prototype and target engineering boundaries
- Last updated: 2026-06-28
- Implementation authority: `index.html`, `style.css`, and `app.js` define current runtime behavior

## System Context

BriefStudio is a client-side creative brief builder. A user selects a Studio, enters campaign information, generates JSON, reviews the output, and sends it to another creative workflow through copy, download, or prompt export.

```text
User
  │
  ▼
BriefStudio browser application
  ├── Studio forms
  ├── Brief generation
  ├── JSON rendering
  └── Export adapters
        │
        ├── Clipboard
        ├── JSON file
        ├── ChatGPT workflow
        └── Midjourney prompt
```

There is currently no BriefStudio backend, database, authentication service, or server-side processing.

## Current Architecture

The application remains a monolithic client-side application, but delivery concerns are separated into three root files:

```text
index.html
├── Document metadata and external font links
├── Desktop and mobile navigation
├── Seven mode panels
├── Shared output panel
└── Export modal and toast region

style.css
├── Design tokens
├── Layout and component styles
└── Responsive rules

app.js
├── Global state
├── DOM interaction helpers
├── Studio-specific generators
├── Dynamic Carousel and Grid editors
├── JSON renderer
└── Copy/download/export functions
```

The extraction creates cacheable file boundaries only. It does not yet create JavaScript modules, reusable components, a Studio registry, or a unified JSON engine.

### Current runtime flow

1. `DOMContentLoaded` initializes Carousel slides and Grid tiles.
2. Mode navigation switches the active `.mode-panel`.
3. Form state is held primarily in DOM elements.
4. Carousel and Grid maintain additional mutable arrays.
5. A `generate*JSON` function queries the active form and constructs an object.
6. `emitBrief` assigns the object to global output state.
7. `renderOutput` serializes and displays the JSON.
8. Export functions consume the same global output object.

### Current state ownership

| State | Owner | Concern |
| --- | --- | --- |
| Standard form values | DOM controls | No typed or centralized state model |
| Current output | Global `cJSON` | Shared by renderer and exporters |
| Current mode | Global `currentMode` plus DOM classes | Two representations of active state |
| Aspect ratios | DOM plus `modeRatio` | Partially duplicated state |
| Carousel slides | Global `slideData` plus rendered inputs | Requires manual synchronization |
| Grid tiles | Global `tileData` plus rendered inputs | Requires rereading DOM before generation |

## Architectural Risks

- A change to one Studio can affect shared output behavior.
- Exporters assume field locations that do not exist in every Studio.
- DOM structure functions as an undocumented data model.
- Global state makes isolated tests difficult.
- Inline events couple templates directly to implementation functions.
- The absence of schema validation permits incompatible JSON.
- UI, refactor, and contract changes cannot be separated cleanly.

## Target Architecture

The target is a modular client-side application that preserves the current lightweight deployment model.

```text
Application Shell
├── Studio Registry
├── Shared State
├── Navigation
└── Output Workspace
    │
    ├── Shared UI Components
    │   ├── Form fields
    │   ├── Toggle groups
    │   ├── Color and ratio controls
    │   ├── Repeatable item editors
    │   └── Dialog, status, and toast
    │
    ├── Studio Modules
    │   ├── Configuration
    │   ├── Initial state
    │   ├── Validation
    │   └── Studio data builder
    │
    └── JSON Engine
        ├── Canonical schema
        ├── Validation
        ├── Legacy adapters
        ├── Summary projection
        ├── Export adapters
        └── Schema migrations
```

## Target Module Responsibilities

### Application shell

- Bootstrap application
- Register supported Studios
- Coordinate navigation
- Own current Studio and current brief state
- Connect Studio modules to output services

The shell must not contain Studio-specific field logic.

### Studio registry

Each entry provides:

- Stable Studio identifier
- Display metadata
- Initial state factory
- Validator
- Canonical brief builder
- Optional Studio-specific renderer or editor
- Compatibility fixtures

### Shared UI components

Shared controls own rendering and interaction semantics, not business rules. They must support keyboard use, accessible naming, validation state, and deterministic state updates.

### Studio modules

Studio modules own only behavior unique to their format. Shared brand, audience, output, and visual concepts must use canonical fragments.

### JSON engine

The engine is the domain boundary between user input and downstream integrations. It must:

- Build a canonical envelope.
- Validate required and conditional fields.
- Remove meaningless empty optional values.
- Preserve stable machine values.
- Produce legacy representations during migration.
- Provide normalized projections to summaries and exporters.

### Export adapters

Each exporter accepts canonical JSON rather than inspecting arbitrary Studio-specific paths. Unsupported exports must return an explicit capability error instead of silently producing degraded output.

## Data Flow Target

```text
User event
  → component event
  → Studio state update
  → validation
  → canonical JSON builder
  → schema validation
  → current brief state
  ├── output renderer
  ├── summary projection
  └── exporter adapter
```

Data flows in one direction. Components do not mutate generated JSON, and exporters do not query form DOM.

## Non-Functional Requirements

### Compatibility

- Existing UI and workflow remain unchanged unless explicitly approved.
- Existing JSON consumers receive a compatibility adapter or documented migration.
- Schema changes require versioning and fixtures.

### Accessibility

- Native semantics are preferred.
- Every control has an accessible name.
- All workflows are keyboard operable.
- Dialogs manage focus.
- Dynamic status uses appropriate live regions.
- Motion respects user preferences.

### Security

- User-controlled data is never inserted as unsanitized HTML.
- External URLs are validated before opening.
- No secrets are stored in client source.
- A production deployment should support a restrictive Content Security Policy.

### Performance

- Initial functionality should remain lightweight.
- Modules and static assets should be independently cacheable.
- Rendering should update the smallest practical region.
- Dependencies require an explicit cost/benefit review.

### Testability

- JSON builders and validators are pure functions.
- Each Studio has representative fixtures.
- Exporters have unit tests for every supported Studio.
- Critical user flows have browser-level tests.

## Architecture Constraints

- Sprint 1 changes documentation only.
- Framework adoption is not part of the initial refactor.
- TypeScript adoption requires a separate decision after module boundaries stabilize.
- Structural refactors must not include a visual redesign.
- The migration must remain deployable after every Sprint.

## Architecture Decisions

Significant decisions are stored in `docs/adr/`.

- [ADR-0001: Retain Vanilla Web Technology and Refactor Incrementally](adr/0001-incremental-vanilla-architecture.md)

## Definition of Architectural Progress

Architecture improves when:

- A Studio can be changed without editing unrelated Studio logic.
- All outputs pass a shared schema validator.
- Exporters operate on canonical fields.
- Shared controls are implemented once.
- State ownership is explicit.
- Tests prove behavior and backward compatibility.
