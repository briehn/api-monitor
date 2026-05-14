# Initial Scope

## MVP Goal
Build a realistic first version of Status Pilot that proves the core monitoring loop works end to end:

`user adds monitor -> manually runs a check -> result is stored -> dashboard shows status/latency/uptime -> scheduled checks follow -> incident is created when failures occur`

The MVP should be small enough to finish, but substantial enough to demonstrate backend systems thinking.

## MVP Scope
- User authentication for a single-account-per-user product
- CRUD for HTTP/HTTPS monitors
- Configurable monitor basics:
  - Name
  - URL
  - Method (`GET` first, expand later if needed)
  - Check interval from a small fixed set
  - Timeout
- Manual "Run check now" flow for a single monitor
- Scheduled checks that run automatically
- Result storage for:
  - Status code
  - Success/failure
  - Response time
  - Checked-at timestamp
  - Error reason when the request fails
- Basic incident detection based on repeated failures
- Dashboard pages that show:
  - Current monitor state
  - Recent check history
  - Basic uptime percentage
  - Open and recent incidents

## MVP Constraints
- Focus on HTTP/API monitoring first, not browser automation
- Start with one monitor type instead of many specialized probe types
- Validate the checker manually before adding scheduler complexity
- Prefer simple scheduling logic before distributed worker infrastructure
- Prefer useful history and incident rules over advanced visualization

## Non-Goals
- Public status pages
- Team accounts and shared workspaces
- On-call routing and escalation policies
- SMS, Slack, PagerDuty, or email alerting in the first milestone
- Region-based multi-location checks
- Synthetic browser checks
- Advanced analytics and long-term retention policies
- Custom domains, billing, or subscription logic

## Product Loop
1. User adds a monitor.
2. The user manually runs a check to validate the HTTP request path.
3. The checker performs the HTTP request.
4. The result is stored with status, latency, and failure metadata.
5. The dashboard reflects current health and recent history.
6. The scheduler later selects that monitor when its next check is due.
7. Repeated failures open an incident.
8. A later successful recovery closes the incident.

## What Makes This MVP Enough
- It demonstrates backend CRUD, scheduled execution, persistence, derived state, and incident logic.
- It creates a clear foundation for later alerting and queue-based workers.
- It stays small enough that core reliability logic can be understood and written by hand.
