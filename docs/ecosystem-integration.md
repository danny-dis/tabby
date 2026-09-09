# Tabby Ecosystem Integration

> **Design rule: better together, never required together.**

Tabby is an independent coding-intelligence product first. It must remain fast, useful, self-hostable, and deployable without DMRX, NOESIS, Ghost Factory, ATHENA, or any other external project.

The wider ecosystem is an **optional capability plane**. When present, it should make Tabby substantially more capable without making Tabby more complicated for the developer.

## 1. The target architecture

Tabby should be the developer-facing coding surface and local software-intelligence runtime. External systems provide capabilities behind stable boundaries.

```text
                         Developer / IDE / CLI
                                  │
                                  ▼
                         ┌──────────────────┐
                         │      TABBY       │
                         │ coding surface   │
                         │ context + UX     │
                         └────────┬─────────┘
                                  │
                    capability / job interfaces
                                  │
              ┌───────────────────┼───────────────────┐
              │                   │                   │
              ▼                   ▼                   ▼
            DMRX                NOESIS          Ghost Factory
       inference routing     durable knowledge    engineering jobs
              │                   │                   │
              └───────────────────┼───────────────────┘
                                  │
                                  ▼
                              ATHENA
                       optional governance
```

The important boundary is that **Tabby owns the coding experience, not the ecosystem**.

## 2. Two equal deployment modes

### Standalone

```text
IDE → Tabby → local/configured inference
             └→ Tabby's repository index/context
```

Everything required for normal completion, chat, search, repository browsing, and configured inference stays local to Tabby.

### Ecosystem-enabled

```text
IDE → Tabby → capability gateway
               ├→ DMRX       (route inference)
               ├→ NOESIS     (durable project knowledge)
               ├→ Ghost      (execute engineering jobs)
               └→ ATHENA     (optional policy/governance)
```

Enabling the ecosystem must not change the basic interaction model. The developer still talks to Tabby.

## 3. Do not duplicate what Tabby already does

Tabby already builds repository context by fetching repositories and related development artifacts, parsing source into an index, and using that context for completion, chat, and search. citeturn0search1

Therefore the ecosystem must **not** introduce another general-purpose code index just because NOESIS exists.

The efficient split is:

| Concern | Owner |
|---|---|
| Hot code context | Tabby |
| Repository indexing | Tabby |
| Symbol/code retrieval | Tabby |
| Interactive completion | Tabby |
| Durable project knowledge | NOESIS |
| Model/provider selection | DMRX |
| Autonomous engineering | Ghost Factory |
| Cross-system governance | ATHENA |

NOESIS should receive **facts, decisions, events, summaries, provenance, and durable knowledge**, not a second copy of every source file unless a deployment explicitly asks for it.

This avoids duplicated storage, duplicated crawling, duplicated embeddings, and conflicting sources of truth.

## 4. One request path, not an agent maze

A major efficiency requirement is to avoid turning every request into a multi-agent orchestration problem.

For normal coding requests, the path should be short:

```text
request
  ↓
classify
  ↓
retrieve minimum useful context
  ↓
select inference backend
  ↓
stream result
```

Only escalate when the task actually requires it:

```text
simple completion/chat
        │
        └──────────────→ Tabby only

complex reasoning
        │
        └──────────────→ DMRX

requires durable project knowledge
        │
        └──────────────→ NOESIS

requires file mutation / tests / CI / PR
        │
        └──────────────→ Ghost Factory

requires ecosystem policy or coordination
        │
        └──────────────→ ATHENA
```

**Do not invoke ATHENA, NOESIS, or Ghost Factory merely because they exist.** Capability invocation must be justified by the request.

## 5. Capability gateway

Tabby should eventually have one small internal abstraction for optional services rather than separate application-wide integrations.

Conceptually:

```rust
trait CapabilityProvider {
    fn capabilities(&self) -> CapabilitySet;
    async fn invoke(&self, request: CapabilityRequest) -> CapabilityResult;
}
```

The exact Rust API should be designed after the existing routes/services/configuration boundaries are mapped. The important property is architectural isolation, not this exact trait.

Providers should be adapters:

```text
Tabby core
   │
   └── CapabilityGateway
         ├── LocalProvider
         ├── DMRXProvider
         ├── NOESISProvider
         ├── GhostFactoryProvider
         └── ATHENAProvider
```

Tabby core should depend on the **capability contract**, never on DMRX/NOESIS/Ghost Factory implementation details.

## 6. Capability discovery

Use discovery rather than hard-coded assumptions.

A provider advertises:

```json
{
  "provider": "dmrx",
  "capabilities": [
    "chat",
    "code_generation",
    "routing",
    "streaming"
  ],
  "protocol_version": "1",
  "limits": {
    "max_context_tokens": 131072
  }
}
```

The actual wire format can evolve, but discovery should answer four questions cheaply:

1. Is the capability available?
2. Which protocol version does it support?
3. What limits apply?
4. What authentication/policy is required?

Cache discovery and health results. Do not perform network capability discovery on every completion request.

## 7. DMRX integration: route, don't proxy blindly

Tabby already has an inference abstraction covering chat, code generation, completion, embeddings, and related configuration. fileciteturn17file0L2-L6

DMRX should therefore appear as an **optional inference provider/router**, not as a second inference abstraction inside Tabby.

Preferred flow:

```text
Tabby
  │ task metadata + context budget
  ▼
DMRX
  │
  ├─ choose local model
  ├─ choose remote model
  ├─ choose specialist
  ├─ enforce privacy policy
  └─ return streaming inference
       │
       ▼
     Tabby
```

Tabby should not need to know why DMRX selected a model.

For standalone operation, Tabby continues using its configured model backend. This preserves the existing self-hosted path; Tabby's server currently exposes serve/download commands and supports multiple local device backends. fileciteturn18file0L2-L2

## 8. NOESIS integration: memory, not a second code browser

NOESIS should provide durable knowledge only when the request benefits from it.

Good examples:

- architecture decisions
- design rationale
- project conventions
- historical incidents
- accepted trade-offs
- recurring bugs
- verified fixes
- deployment constraints
- team/project preferences
- procedural knowledge

Avoid retrieving large memory bundles by default.

Use a small contextual assembly step:

```text
query
  ↓
relevance filter
  ↓
permission filter
  ↓
provenance filter
  ↓
context budget
  ↓
small memory packet
```

Every memory result should carry provenance and confidence. If NOESIS is unavailable or stale, Tabby falls back to its normal repository context.

## 9. Ghost Factory integration: jobs, not embedded agents

Tabby should never grow a second autonomous coding factory.

For tasks that require mutation or long-running verification, Tabby submits a **job**:

```text
Tabby
  │
  │ EngineeringJob
  ▼
Ghost Factory
  │
  ├── inspect
  ├── plan
  ├── modify
  ├── test
  ├── review
  └── produce artifact/PR
  │
  ▼
Tabby event stream
```

Tabby remains responsible for presenting the job, status, diffs, logs, and results. Ghost Factory owns execution.

This keeps Tabby responsive and prevents long-running autonomous work from consuming the interactive request path.

## 10. ATHENA integration: governance only when needed

ATHENA should not sit in the hot path of every completion.

Use it for events that actually require ecosystem-level decisions:

- authorization beyond Tabby's local policy
- high-risk operations
- cross-agent coordination
- resource arbitration
- multi-system workflows
- deployment-wide policy

A normal completion should not become:

```text
Tabby → ATHENA → DMRX → NOESIS → Ghost Factory → ATHENA → model
```

That would destroy latency and create unnecessary failure points.

Instead:

```text
normal request → Tabby → model

escalated request → Tabby → capability → required subsystem
```

## 11. Context efficiency

Context is one of the biggest performance costs in coding agents.

Tabby should assemble context in layers:

```text
L0  current edit / cursor context
L1  nearby symbols
L2  relevant files
L3  repository relationships
L4  durable project memory
L5  external documentation
```

Start at L0 and expand only when confidence is insufficient.

Never send the whole repository simply because it is available.

Use:

- symbol-level retrieval
- structural relationships
- recent changes
- dependency edges
- query-specific ranking
- deduplication
- token budgets
- incremental context expansion
- cancellation when the answer is already sufficiently grounded

This is particularly important because Tabby already performs repository indexing and retrieval; the ecosystem should enrich retrieval rather than blindly append more text. citeturn0search1

## 12. Hot path vs cold path

Separate interactive work from asynchronous work.

### Hot path

- completion
- chat
- code search
- context retrieval
- streaming inference

Requirements:

- low latency
- bounded context
- aggressive caching
- cancellation
- no unnecessary network calls
- no autonomous jobs

### Cold path

- repository re-indexing
- NOESIS consolidation
- architecture extraction
- telemetry aggregation
- long-running refactors
- test suites
- CI investigation
- PR generation

Requirements:

- durable jobs
- retries
- checkpointing
- resource scheduling
- resumability

This separation is essential for keeping the interactive Tabby experience fast.

## 13. Caching strategy

Use layered caches rather than one giant cache.

```text
L1 request cache
L2 context/retrieval cache
L3 repository/index cache
L4 provider/model metadata cache
L5 durable knowledge (NOESIS)
```

Cache keys should include relevant repository revision, configuration, model/provider, and context policy so stale context cannot silently leak into new requests.

Do not cache sensitive external inference results unless the deployment explicitly permits it.

## 14. Streaming and cancellation

All optional network capabilities should support:

- streaming where useful
- deadlines
- cancellation
- bounded retries
- idempotency for jobs
- correlation IDs
- structured events

Interactive inference should be cancellable immediately when the user changes the request or editor state.

Long-running Ghost Factory jobs should survive client disconnects and expose a durable job ID.

## 15. Failure isolation

Optional services should use circuit breakers and health state.

```text
healthy      → use capability
suspect      → prefer local fallback
unavailable  → skip capability
recovering   → probe asynchronously
```

Failure must not cascade:

```text
NOESIS down
   ✗ should not break
       ↓
Tabby completion
```

Likewise, DMRX being unavailable must not prevent a standalone configured model from serving requests.

## 16. Security model

Every capability request should have an explicit scope.

At minimum:

```text
identity
project
repository
operation
requested capability
resource scope
risk level
expiry/deadline
```

High-risk operations such as repository-wide mutation, external deployment, secrets access, or destructive operations should require stronger authorization than read-only context retrieval.

External inference must never silently bypass Tabby's privacy configuration.

## 17. Observability without telemetry duplication

Tabby already has OpenTelemetry-related infrastructure in the workspace. fileciteturn19file0L2-L2

Do not build a second observability stack into the ecosystem adapter.

Emit a common correlation ID across:

```text
Tabby request
   ↓
capability invocation
   ↓
DMRX / NOESIS / Ghost / ATHENA
   ↓
result
```

Record latency, outcome, capability, provider, token/context metrics, and error class while respecting privacy policies.

## 18. What should NOT be built

To keep the system streamlined, avoid:

- embedding ATHENA into Tabby's core
- duplicating NOESIS's memory engine
- duplicating Ghost Factory's autonomous agent loop
- creating another model router inside Tabby
- making every request pass through the ecosystem
- copying the entire repository into every subsystem
- introducing a new message bus for simple request/response calls
- adding a dependency on a specific ecosystem project
- forcing ecosystem configuration on standalone users
- creating a second UI for ecosystem administration

The ecosystem should be **thin at the integration boundary and powerful behind it**.

## 19. Implementation phases

### Phase 1 — contracts

- capability discovery schema
- capability invocation schema
- health/version endpoint
- correlation IDs
- timeout/cancellation semantics
- authentication model
- error model

No DMRX/NOESIS/Ghost Factory dependency in core.

### Phase 2 — DMRX adapter

Add an optional provider adapter that treats DMRX as a model-routing backend.

Success criteria:

- standalone inference unchanged
- streaming preserved
- no extra network hop when DMRX is disabled
- automatic fallback
- measurable latency overhead

### Phase 3 — NOESIS adapter

Add selective durable-memory retrieval and write-back.

Success criteria:

- no duplicate source-code index
- strict context budget
- provenance on memory
- permission filtering
- graceful fallback

### Phase 4 — Ghost Factory jobs

Add asynchronous engineering jobs and event streaming.

Success criteria:

- Tabby never owns the execution loop
- jobs survive client disconnects
- cancellation works
- results appear as normal Tabby artifacts

### Phase 5 — ATHENA governance

Add optional policy checks for operations that genuinely need ecosystem governance.

Success criteria:

- no ATHENA dependency for ordinary requests
- policy latency is bounded
- policy failures fail closed for high-risk operations and fail open only where explicitly safe

## 20. Performance goals

The integration should be measurable, not architectural theater.

Track at least:

- p50/p95 completion latency
- p50/p95 chat latency
- context assembly latency
- retrieval latency
- tokens sent/received
- cache hit rate
- external capability overhead
- fallback rate
- cancellation rate
- Ghost Factory job duration
- memory retrieval usefulness

The first optimization target is simple:

> **If enabling the ecosystem makes ordinary Tabby completion noticeably slower, the integration is wrong.**

## 21. Product boundary

Tabby remains independently valuable:

> **A self-hosted coding intelligence environment.**

The ecosystem adds optional depth:

```text
Tabby alone
    ↓
excellent coding assistant

Tabby + DMRX
    ↓
adaptive model intelligence

Tabby + NOESIS
    ↓
persistent project intelligence

Tabby + Ghost Factory
    ↓
autonomous software engineering

Tabby + ATHENA
    ↓
governed participation in a larger agent system

All together
    ↓
full software-engineering intelligence surface
```

The key is that each addition is **composable, replaceable, observable, and optional**.

Tabby should become better because the ecosystem exists — never because Tabby requires it.
