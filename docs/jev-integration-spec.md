# Jev Integration Specification

## Summary
Uses Jev to classify coding tasks and route them before invoking coding/reasoning models.

## Implementation
- Classify task type, language, repository scope, complexity, context depth, test requirement, and tool requirement.
- Route simple bounded work to lightweight models/tools and reserve deep models for complex work.
- Use Jev for candidate ranking but retain deterministic workspace permissions.
- Integrate through DMR-X.
- Add route latency/outcome telemetry and a representative coding-task benchmark corpus.
- Support local decision backends.

## Acceptance criteria
- Tabby works without Jev.
- No repository permission is granted from model output.
- Routing decisions are auditable.
