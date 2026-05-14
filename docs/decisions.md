# Early Decisions

## Why This Project Exists
Status Pilot exists to demonstrate backend engineering skills through a monitoring domain: scheduling, persistence, incident logic, reliability tradeoffs, and operational thinking.

## Why It Complements Track My Apps
Track My Apps already covered a broader full-stack SaaS surface area. Status Pilot should show a different strength profile:
- backend systems
- background execution
- monitoring data
- incident modeling
- reliability-oriented design

Together, the two projects present a more complete engineering portfolio.

## Why MVP Starts Simple
The first version should prove the monitoring loop before layering on infrastructure complexity. A simple, correct system is more useful for learning than an ambitious architecture that is only partially understood.

## Why Background Jobs and Queues May Come Later
Workers and queues are important to the long-term design, but they should follow working core logic. First validate:
- manual check execution
- monitor scheduling
- HTTP check execution
- result persistence
- incident opening and closing

The manual "run check now" flow comes before scheduling so the checker can be learned and verified in isolation. That keeps the first automation step small and reduces the risk of baking scheduler assumptions into the core request path.

After those are stable, introducing a queue becomes a meaningful architectural improvement instead of premature complexity.

## Likely Stack Choices
These are directionally likely, not locked:
- Web app framework: Next.js
- Language: TypeScript
- ORM: Prisma
- Database: PostgreSQL
- Auth: a standard Next.js-compatible auth solution
- Scheduling: start simple, then evaluate worker or queue-backed execution
- Deployment: Vercel for app plus a worker-friendly platform later if needed

## Decision Rule
Choose the simplest option that still teaches the intended backend concept. Avoid infrastructure choices that add operational weight before they add learning value.
