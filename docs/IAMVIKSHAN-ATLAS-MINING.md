# iamvikshan Atlas Mining → TABBY

Status: **architecture input for TABBY**

Source: iamvikshan/atlas

## Decision

TABBY should adopt the **developer-side orchestration discipline** of iamvikshan/Atlas while remaining a lightweight physical coding assistant.

The useful part is not its Greek-agent naming. The useful part is the strict separation between:

- intent
- planning
- specialist execution
- research/scouting
- adversarial review
- lifecycle hooks
- skills
- verification

TABBY should use these ideas to make its coding assistance more reliable without turning TABBY into Ghost Factory.

## Mined architecture

### IntentGate

Before TABBY turns a request into an edit, it should classify:

- intent
- target surface
- requested scope
- confidence
- destructive/consequential nature
- required context
- required tools
- whether research is needed
- whether the user asked for explanation, suggestion or actual mutation

The important behavior is: **do not implement the first plausible interpretation**.

For ambiguous requests TABBY should produce a compact clarification or a structured interpretation, rather than silently choosing a large refactor.

### Strict routing

TABBY should have capability categories such as:

- completion
- explanation
- navigation
- refactoring
- test generation
- debugging
- architecture
- documentation
- security review
- research

Routing is deterministic first.

Example:

`rename symbol -> LSP/refactor capability`

rather than asking an LLM to invent a textual replacement.

### Read-before-edit

Adopt this as a hard TABBY editing invariant:

> A file must be read/understood before an agent can mutate it.

For symbol-level operations, the preferred context should be LSP/tree-sitter structural context rather than a raw full-file dump.

### Worker separation

TABBY should expose specialist modes/capabilities rather than one monolithic coding persona:

- Scout: locate relevant symbols/files
- Analyst: explain current behavior
- Builder: propose/apply changes
- Tester: generate/run targeted tests
- Reviewer: adversarially inspect changes
- Researcher: investigate external APIs/frameworks
- LSP specialist: perform structural code intelligence

TABBY itself may orchestrate these lightweight roles locally or delegate deeper work to an external agent.

### Adversarial reviewer

The Atlas `sentry` pattern is valuable.

A reviewer should not share the builder's success assumption.

Review questions:

- Did the change satisfy the actual intent?
- Was the correct symbol/file modified?
- Are edge cases missing?
- Does the change violate local conventions?
- Are tests meaningful?
- Did the model make unsupported claims?
- Did the change introduce security/privacy problems?
- Is the change larger than requested?

Reviewer output should be structured evidence, not another prose opinion.

### Lifecycle hooks

TABBY should expose hooks around assistant actions:

- session start
- prompt submit
- context assembly
- pre-edit
- post-edit
- pre-command
- post-command
- pre-apply
- post-apply
- pre-compaction
- session stop

Hooks should support allow/block/modify/ask.

This gives TABBY a clean enforcement surface for future security and AETHER integration.

### Skills

Skills should be explicit reusable procedures:

- debugging
- test repair
- framework migration
- SQL optimization
- LSP navigation
- Git workflows
- project-specific conventions

Skills are loaded when needed rather than permanently occupying context.

TABBY should also be able to consume evolving skills produced by the ecosystem's capability-evolution systems.

## TABBY + LSP intelligence

This mining strengthens an existing design direction:

TABBY's LSP layer should be the **deterministic code intelligence substrate**.

The agent should ask LSP/tree-sitter questions such as:

- where is this symbol defined?
- what references it?
- what type does it have?
- what implements this interface?
- what imports this module?
- what diagnostics exist?
- what rename operation is structurally valid?

The model should reason over these facts rather than pretending to be the compiler.

## Verification loop

For an edit:

`Intent`
→ `Context/LSP`
→ `Plan`
→ `Edit`
→ `Diagnostics`
→ `Targeted Tests`
→ `Adversarial Review`
→ `Apply / Revert`

The user remains the final authority over mutation.

## What TABBY must NOT inherit

TABBY should not become:

- a second Ghost Factory
- a long-running autonomous software factory
- an agent fleet manager
- a replacement for DMR-X
- a replacement for NOESIS
- a replacement for ATHENA

TABBY is the **physical coding interface and local coding intelligence layer**.

## Future DMR-X integration

TABBY should request capabilities rather than model names.

Example:

`capability=coding.reasoning`
`context=LSP+diff+tests`
`latency=interactive`
`privacy=local-preferred`
`budget=interactive`

DMR-X chooses the model/runtime.

This lets TABBY benefit from Inferstep-style search/verification without embedding a specific inference engine.
