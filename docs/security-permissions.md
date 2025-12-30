# Security Permissions

## Purpose

Define the permission model for SVSD, mapping roles, process states, and allowed actions.

## Roles

- **Actor**: UI renderer or service sending intents.
- **Provider**: Source of truth, final decision-maker.
- **Gateway**: Policy Enforcement Point, intermediate validator.

## Permissions

- Each action is allowed only if the combination `(role, processState, action)` is valid.
- All intents must include a `schemaHash`.
- Gateway filters and enforces permissions before forwarding actions to Provider.

## Invariants

1. Immutable schema per `(processInstanceId, taskId)`.
2. Two-phase validation: Gateway (shape + permission) → Provider (semantics).
3. Audit: log `processId`, `taskId`, `actionId`, `schemaHash`, `userId`, result.
4. Idempotency: each action must contain `idempotencyKey`.
