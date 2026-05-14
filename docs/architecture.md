# Architecture

## Goal
Status Pilot should be designed as a backend-focused system, even if the user-facing product includes a web app. The main value of the project is not the UI. The main value is reliable scheduled checking, result storage, state transitions, and incident handling.

## Core Components

### Frontend App
- Lets users sign in
- Lets users create and manage monitors
- Displays current health, latency history, and incidents
- Calls backend APIs for monitor data and derived dashboard views

### Backend API
- Owns auth-protected CRUD operations
- Validates monitor inputs
- Exposes a manual "run check now" action early in the project
- Exposes dashboard and history data
- Coordinates monitor state changes and incident views

### Database
- Stores users, monitors, check results, incidents, and later alert settings
- Acts as the source of truth for current monitor state and historical records

### Scheduler / Worker
- Finds monitors that are due for checking
- Triggers monitor checks on a schedule
- Persists results
- Evaluates whether an incident should open or close
- Should reuse the same checker path as the manual check flow

### Monitor Checker
- Executes HTTP requests with timeout handling
- Measures latency
- Captures status code, success/failure, and failure reason
- Returns normalized result data to the system

### Incident Detection
- Applies simple rules to determine when failures become incidents
- Tracks incident start and recovery time
- Separates raw check results from higher-level outage events

### Alerting Later
- Not required for the earliest MVP
- Should be designed as a downstream step after incident transitions are trustworthy

## MVP-Good vs Production-Better

### MVP-Good
- Single app repository
- One relational database
- A simple scheduled job that runs inside the app or a basic worker process
- Direct database polling for due monitors
- Basic retry/error handling
- Simple incident logic such as "open after N consecutive failures"

### Production-Better
- Separate API and worker services
- Durable queue between scheduler and workers
- Dedicated scheduler service
- Multiple worker instances
- Region-aware checks
- Better retry policies, dead-letter handling, and alert delivery guarantees
- Stronger observability around job throughput and failure rates

## Suggested MVP Flow
1. User creates a monitor from the frontend.
2. Backend stores the monitor with interval and timeout metadata.
3. User manually runs a check to validate checker behavior.
4. Result is written to `CheckResult`.
5. Dashboard queries current monitor state plus recent history.
6. Scheduler later queries for due monitors.
7. Worker or scheduled task executes the same check path.
8. Monitor summary fields are updated.
9. Incident detection logic evaluates recent outcomes.

## Why This Project Is Backend-Focused
- The interesting problems are scheduling, failure handling, state transitions, and historical data.
- Reliability logic is more important than UI complexity here.
- It complements Track My Apps by showing systems thinking instead of another feature-heavy SaaS frontend.
- The strongest portfolio signal will come from clean domain modeling, job design, incident rules, and observability decisions.

## Likely Evolution Path
1. App with scheduled checks in-process
2. Extract check execution into a worker
3. Introduce a queue for durability and scale
4. Add alert delivery and operational metrics
