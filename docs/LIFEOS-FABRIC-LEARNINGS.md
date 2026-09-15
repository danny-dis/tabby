# LifeOS + Fabric Learnings for TABBY

TABBY remains the user-facing coding/learning agent, but adopts a few ecosystem-wide patterns.

## Intent-aware coding

Where useful, TABBY should understand the requested outcome and acceptance criteria rather than treating every request as an isolated prompt.

## Ideal State / acceptance criteria

Coding tasks should be representable as observable desired state plus tests or evidence that demonstrate completion.

## Capability contracts

Reusable coding operations should be expressed as capabilities that can be routed through DMR-X instead of binding TABBY to one model.

## Verification

TABBY should distinguish generated changes from verified changes and surface test/build/lint evidence to the user.

## Shared ecosystem state

TABBY can consume NOESIS context and DANNY/ATHENA capabilities when authorized, but remains a user-facing coding interface rather than the ecosystem's governance or memory authority.
