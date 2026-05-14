# Schema Direction

This document proposes the initial data model direction before a real Prisma schema is written.

## Design Goals
- Keep the first schema small and understandable
- Separate raw check history from incident records
- Store enough monitor summary data to make dashboard queries simple
- Allow the system to grow into alerts and workers later

## Proposed Models

### User
Represents an authenticated account owner.

Likely fields:
- `id`
- `email`
- `name`
- `createdAt`
- `updatedAt`

Relationships:
- One user has many monitors
- One user may later have many notification channels or alert rules

### Monitor
Represents one website or API endpoint being checked.

Likely fields:
- `id`
- `userId`
- `name`
- `url`
- `method`
- `expectedStatusCode` or simple success rules
- `intervalMinutes`
- `timeoutMs`
- `active`
- `lastCheckedAt`
- `nextCheckAt`
- `lastStatus`
- `lastLatencyMs`
- `consecutiveFailures`
- `createdAt`
- `updatedAt`

Relationships:
- Belongs to one user
- Has many check results
- Has many incidents

Notes:
- Summary fields on the monitor help the dashboard avoid expensive aggregation on every request.
- `consecutiveFailures` supports simple incident logic in the MVP.

### CheckResult
Represents one execution of one monitor check.

Likely fields:
- `id`
- `monitorId`
- `status`
- `statusCode`
- `latencyMs`
- `checkedAt`
- `errorMessage`
- `responseSnippet` optional and probably skipped at first

Relationships:
- Belongs to one monitor

Notes:
- `status` can be a small enum such as `SUCCESS`, `FAILURE`, `TIMEOUT`.
- This is the time-series backbone of the project.

### Incident
Represents a meaningful outage window rather than a single failed check.

Likely fields:
- `id`
- `monitorId`
- `startedAt`
- `resolvedAt`
- `status`
- `failureCountAtOpen`
- `recoveryCheckResultId` optional
- `summary`
- `createdAt`
- `updatedAt`

Relationships:
- Belongs to one monitor

Notes:
- MVP logic can open an incident after repeated failures and resolve it after a successful recovery check.
- Keep incident records distinct from check results so outages are easy to query and explain.

### AlertRule or NotificationChannel
Optional for the initial schema decision. Reasonable to postpone until alerts are actually implemented.

If introduced early, prefer `NotificationChannel`.

Likely fields:
- `id`
- `userId`
- `type`
- `target`
- `enabled`
- `createdAt`
- `updatedAt`

Relationships:
- Belongs to one user

Notes:
- This should not block the first monitoring loop.

## Relationship Summary
- `User 1 -> many Monitor`
- `Monitor 1 -> many CheckResult`
- `Monitor 1 -> many Incident`
- `User 1 -> many NotificationChannel` later if alerts are added

## Important Modeling Choices

### Current State vs Historical Records
- `Monitor` stores current summary state
- `CheckResult` stores every individual check
- `Incident` stores derived outage periods

This separation keeps reads simple and preserves history without overcomplicating the MVP.

### Time-Series Pragmatism
- Start with regular relational tables
- Do not introduce specialized time-series infrastructure yet
- Add indexes later on `monitorId`, `checkedAt`, and incident status/time fields

### Incident Simplicity
- Start with deterministic rules
- Example: open after 3 consecutive failures, resolve on first success
- Improve the rules only after the basic monitoring loop is stable
