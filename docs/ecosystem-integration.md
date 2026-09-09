# Tabby Ecosystem Integration

## Principle

Tabby is an independent project first.

It must remain useful as a self-hosted AI coding assistant without requiring DMRX, NOESIS, ATHENA, Ghost Factory, or any other external system. Integrations with a broader agent ecosystem are optional capabilities that make Tabby more powerful when those services are available.

The goal is **better together, never required together**.

## What Tabby owns

Tabby remains responsible for the developer-facing coding experience and its existing coding-intelligence capabilities, including:

- IDE/editor interaction
- code completion and generation
- repository context and indexing
- code search and browsing
- chat and developer assistance
- local/self-hosted inference
- existing HTTP/OpenAPI interfaces

External systems should not make Tabby responsible for global orchestration or autonomous execution.

## Optional ecosystem capabilities

When present, Tabby can discover and use external capabilities through stable interfaces.

### DMRX — model routing

DMRX can provide model selection and inference routing when a deployment wants access to multiple local, remote, or specialized models.

Tabby should request a capability rather than depend on a particular model or provider. If DMRX is unavailable, Tabby falls back to its configured model backend.

Potential capabilities:

- model selection
- provider routing
- task-aware inference
- model benchmarking metadata
- cost/latency-aware selection
- privacy-aware routing
- streaming inference

### NOESIS — project memory

NOESIS can provide persistent project knowledge when available.

Tabby should retain its own operational context and must not require NOESIS for basic functionality. NOESIS can enrich that context with durable knowledge such as:

- architectural decisions
- project conventions
- historical changes
- known constraints
- recurring bugs and fixes
- developer/project preferences
- semantic and procedural project knowledge

The integration should be read/write through an explicit interface with provenance, permissions, and lifecycle semantics rather than coupling Tabby's internal storage directly to NOESIS.

### Ghost Factory — software engineering execution

Ghost Factory can extend Tabby from interactive assistance into autonomous software-engineering workflows.

Examples include:

- implement an issue
- perform a repository-wide refactor
- migrate a framework or dependency
- generate and update tests
- run verification loops
- inspect CI failures
- prepare a pull request
- perform controlled maintenance

Tabby should initiate these operations as capabilities/jobs. Ghost Factory remains responsible for execution, verification, and autonomous engineering workflows.

### ATHENA — orchestration and governance

ATHENA can govern Tabby participation in a larger agent ecosystem when a deployment chooses to use it.

Tabby should not become an ATHENA implementation or global orchestrator. ATHENA may provide policy, authorization, routing, priority, resource, risk, or coordination decisions through explicit interfaces.

## Capability discovery

The preferred integration pattern is capability discovery rather than hard-coded service dependencies.

Conceptually:

```text
Tabby
  │
  ├── local capabilities
  │     ├── completion
  │     ├── chat
  │     ├── repository context
  │     └── configured inference
  │
  └── optional capabilities
        ├── model routing      → DMRX
        ├── persistent memory  → NOESIS
        ├── engineering jobs   → Ghost Factory
        └── governance         → ATHENA
```

A deployment should be able to start with only Tabby and progressively add capabilities without changing the fundamental developer workflow.

## User experience

The ecosystem should be invisible unless the user needs it.

A developer can simply use Tabby for:

> Explain this function.

> Complete this code.

> Find where authentication is implemented.

With optional services enabled, the same interface can support:

> Why was authentication implemented this way?

> Refactor authentication across the repository.

> Upgrade this project to the latest framework version and open a PR.

The user should not need to understand which subsystem handled the request.

## Isolation and failure behavior

Optional integrations must fail gracefully.

- DMRX unavailable → use Tabby's configured inference backend.
- NOESIS unavailable → use Tabby's normal repository/context mechanisms.
- Ghost Factory unavailable → keep the task interactive or report that autonomous execution is unavailable.
- ATHENA unavailable → continue in standalone mode unless a deployment explicitly requires governance.

No optional integration should silently make a standalone Tabby installation unusable.

## Security boundaries

External capabilities must be explicitly authorized. Integrations should support:

- capability discovery
- authentication and authorization
- scoped project/repository access
- explicit tool permissions
- audit/event records
- streaming events where appropriate
- cancellation and timeout handling
- policy-controlled external inference

In particular, model routing and memory integrations must not bypass Tabby's security and privacy boundaries.

## Architectural direction

The long-term direction is for Tabby to become a reusable **coding intelligence surface** while remaining independently deployable.

```text
                    Developer
                        │
                        ▼
                    ┌───────┐
                    │ Tabby │
                    └───┬───┘
                        │
             capability interfaces
                        │
       ┌────────────────┼────────────────┐
       ▼                ▼                ▼
     DMRX            NOESIS        Ghost Factory
   model routing      memory       engineering
       │                │                │
       └────────────────┼────────────────┘
                        ▼
                     ATHENA
                 optional governance
```

This architecture preserves two valid deployment modes:

1. **Standalone Tabby** — a complete self-hosted coding assistant.
2. **Ecosystem Tabby** — the same coding assistant augmented by external routing, memory, engineering, and governance capabilities.

The second mode should add power, not introduce lock-in.