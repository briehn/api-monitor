# Roadmap

## Phase 1: Setup and Foundation
- Define project scope and architecture
- Choose initial stack
- Set up repository structure
- Set up baseline documentation
- Decide local development workflow

## Phase 2: Auth and Monitor CRUD
- Add authentication
- Create user-owned monitor model
- Build monitor create/read/update/delete flows
- Validate URLs, intervals, and timeout inputs

## Phase 3: Manual "Run Check Now"
- Add a manual check action for a single monitor
- Use it to validate the HTTP checker before scheduling logic exists
- Store the first check result shape with latency and failure metadata
- Learn the core check flow before adding automation complexity

## Phase 4: Check History and Dashboard
- Build backend queries for monitor health summaries
- Show recent checks and response times
- Show uptime percentage for a simple window
- Build the first useful dashboard views

## Phase 5: Scheduled Checks
- Implement scheduler logic for due monitors
- Reuse the manual checker logic for automatic runs
- Track monitor summary state such as last status and next check time
- Keep scheduled execution simple before adding worker infrastructure

## Phase 6: Incident Detection
- Add deterministic incident rules
- Open incidents after repeated failures
- Resolve incidents after recovery
- Show incident history in the dashboard

## Phase 7: Alerts
- Add notification channel modeling
- Trigger alerts on incident open and resolve
- Start with one delivery path first
- Add simple alert preferences

## Later Phases
- Queue-backed worker execution
- Separate worker process from the app
- Charts and richer historical visualization
- Public status pages
- Team and workspace features
- Multi-region or multi-probe checks
- Cloud deployment and production observability
- Rate limiting, retries, and resilience improvements

## Roadmap Principle
Each phase should leave the product usable, testable, and easier to reason about than the phase before it. Do not jump to distributed infrastructure before the core monitoring logic is correct.
