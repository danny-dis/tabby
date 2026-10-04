# Four Reference Systems — TABBY Integration Mining

Status: architecture input for TABBY; complements docs/IAMVIKSHAN-ATLAS-MINING.md
Date: 2026-10-05

## Boundary
TABBY remains the physical coding assistant and local coding-intelligence layer. It is not Ghost Factory, DMR-X, NOESIS or ATHENA.

## ATLAS·OS → project-state awareness
Use deterministic project-state detection before selecting a coding action. Inspect repository state, active file/symbol, diagnostics, tests, git state, project instructions and current user intent.

Route from state -> eligible coding operation -> capability. Do not ask a model to rediscover state that LSP, Git, tree-sitter or filesystem inspection can provide.

Use phase-aware lightweight roles for larger tasks: scout, architect, implementer, tester, reviewer and release helper.

## Pacifio Atlas → coding provenance and handoff
Record session, checkpoint, tool call, file change, diff, test result and model/runtime provenance for meaningful coding work.

A checkpoint should support handoff to another model or agent with intent, current state, constraints, files/symbols touched, known failures, last verified point and next action.

Keep raw provenance outside the normal conversational context. Generate compact context slices from it.

## Inferstep ATLAS → adaptive coding effort
For trivial completions use one pass. For difficult multi-file or uncertain changes, increase candidate diversity and verification through DMR-X.

Useful strategy progression: direct -> deterministic checks -> alternative plans -> independent candidates -> adversarial verification -> bounded repair.

TABBY owns coding-specific evaluation signals such as compiler diagnostics, targeted tests, diff scope and LSP correctness. DMR-X chooses inference strategy and model/runtime.

## iamvikshan Atlas → hard coding discipline
Keep IntentGate, strict capability routing, read-before-edit, specialist roles, adversarial review and lifecycle hooks.

Use LSP/tree-sitter as the deterministic substrate for symbol definitions, references, types, implementations, diagnostics and safe refactors.

Builder changes should be reviewed independently. A review result is structured evidence, not a second autocomplete.

## Target loop
Intent -> Context/LSP -> Plan -> Edit -> Diagnostics -> Tests -> Adversarial Review -> Apply/Revert

## Safety
Pre-edit and pre-command hooks should be able to allow, block, modify a bounded request or require user approval.

Never allow a coding model to widen file, shell, network or credential permissions by changing its own prompt or tool request.

## Tests
Verify read-before-edit, LSP-grounded edits, bounded diff scope, reviewer independence, deterministic hooks, checkpoint resume and adaptive escalation after repeated failures.

## Non-goals
Do not turn TABBY into a software factory, model router, memory OS or agent-fleet manager.

## Result
TABBY becomes a more disciplined physical coding environment: deterministic code intelligence first, adaptive model help second, independent verification before trusted mutation.