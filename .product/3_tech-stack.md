---
project: ai-ready-product-docs
revision: 1
revision_last_date: 09/28/26
approved: no
approved_acceptance: no existing conflict
ready_for_work: yes
ready_for_work_acceptance: partially usable
---

# Technical Choices — AI-Assisted Student Support Ticketing

_Define the technologies, architecture, and technical decisions required to support the MVP, explaining why they were selected and what trade-offs they introduce._

> Feature references (M1–M9, S1–S9) point to the [MVP](./2_mvp.md).

## Technical Requirements
**What technical capabilities and constraints must our solution satisfy?**

- **Self-hosted:** we deploy and run our own application, packaged with Docker, on an external cloud provider (free tier preferred); no dependency on a SaaS ticketing platform.
- **Always-on background work:** email polling and AI jobs run continuously, so the hosting must not put the worker to sleep.
- **Email intake (M1):** receive support emails, turn each into a ticket, and send replies back by email.
- **AI processing (M2, M5, M6, M7):** classification, summaries, suggested replies, and knowledge-base-grounded responses, without blocking email intake or the UI.
- **Knowledge base (M2):** store the support handbook in a form the AI can use as its source of truth.
- **Web application for staff (M3, M4, M9):** ticket list with filtering and sorting, ticket detail view, and a dashboard.
- **Authentication and authorization (M8):** accounts, database-backed sessions, and role-based access (admin-only user management).
- **Testability:** the full flow, from an incoming email to a reply, must be testable end to end in a local environment.

## Architecture
**How should the different components of the system be structured and interact with each other?**

```
 Student email ──► Mail server ──► Email ingestion ──► Database ◄── Web API ◄── Staff web app
                                         │                │  ▲                  (list, detail,
                                         ▼                ▼  │                   dashboard)
                                    Job queue ──► AI worker ─┘
                                                     │
                                                     ▼
                                               Claude API
```

- **Staff web app:** single-page app used by agents and admins.
- **Web API:** REST endpoints for tickets, users, and AI actions; enforces authentication and roles.
- **Email ingestion:** polls the support mailbox and creates tickets. Outgoing replies are sent through a transactional email provider (Mailgun or SendGrid).
- **Job queue and AI worker:** each new ticket enqueues AI jobs (classify, summarize, generate response). Running them in the background keeps intake fast and lets failed AI calls be retried.
- **Database:** single source of truth for tickets, messages, users, AI outputs, and the knowledge base.

Everything is packaged as Docker containers and deployed to one cloud host for the MVP.

The code lives in a single repository with three top-level workspaces:

- **`client/`:** the staff web app.
- **`server/`:** the Web API, email ingestion, and the AI worker.
- **`shared/`:** contracts used by both sides, such as validation schemas and types.

## Technology Stack
**Which technologies, frameworks, libraries, and services will we use?**

| Layer | Proposed choice | Notes |
| --- | --- | --- |
| Repository | Monorepo with npm workspaces: `client/`, `server/`, `shared/` | One repository for the whole application; `shared/` holds code used by both client and server (see its README) |
| Language | TypeScript (front and back end) | One language and shared types across the stack |
| Frontend | React + Vite | Same toolchain the team already uses |
| UI components | Tailwind CSS + shadcn/ui | Tables, filters, and forms for the list and dashboard |
| Client-side routing | React Router | Pages for the ticket list, ticket detail, dashboard, user management, and login; route guards hide admin-only pages. Filters and sorting kept in the URL so list views can be shared and bookmarked. |
| Data fetching | TanStack Query | Caching and refetching for the ticket list and detail views |
| Forms and validation | React Hook Form + Zod | Forms for user management and agent replies; the same Zod schemas validate input on the API |
| Backend | Node.js + Express | Simple, widely known REST API |
| Database | PostgreSQL | Relational data (tickets, users, roles); self-hostable |
| ORM | Prisma | Schema, migrations, and type-safe queries |
| Job queue | pg-boss (on PostgreSQL) | Background AI jobs and retries without adding Redis |
| Authentication | Database sessions: a session table in PostgreSQL, referenced by an HTTP-only cookie, managed by a maintained auth library (e.g. Better Auth) | Sessions can be revoked instantly (logout, deactivated user); role checks enforced in the API |
| Email in | IMAP polling (e.g. ImapFlow) | Works with any self-hosted or existing mailbox |
| Email out | Mailgun (proposed) or SendGrid, via its API | Handles deliverability (SPF/DKIM signing, bounces). Check current free-plan terms before choosing: they change often. Plain SMTP (Nodemailer) remains a fallback. |
| AI | Claude API via the Anthropic SDK | Faster model (Claude Haiku) for classification and summaries; stronger model (Claude Sonnet) for responses and suggested replies |
| Knowledge base | Markdown documents stored in the database, included in the prompt | Small handbook fits in the prompt; prompt caching keeps cost down |
| Containers | Docker + Docker Compose | Same images locally, in CI, and in production |
| Hosting | Oracle Cloud Always Free VM (proposed) | Always-on VM that runs the full Docker Compose stack, including PostgreSQL, at no cost. Verify current free-tier limits. |
| Unit and API tests | Vitest + Supertest | |
| End-to-end tests | Playwright + Mailpit | Mailpit is a local mail server, so tests can send a real email and check the reply |

## Alternatives Considered
**What other technical options have we evaluated?**

- **Full-stack framework (Next.js) instead of React + Express:** fewer moving parts, but mixes server and client concerns and adds framework conventions that are unnecessary for an internal staff app.
- **Inbound email webhook (Mailgun Routes, SendGrid Inbound Parse) instead of IMAP polling:** instant delivery and pre-parsed messages, and it reuses the outbound provider. It requires the API to be reachable at all times; worth reconsidering once hosting is settled.
- **Plain SMTP instead of Mailgun/SendGrid for outbound:** no extra provider, but we would own deliverability (DNS signing, bounces, reputation).
- **JWT (stateless tokens) instead of database sessions:** no session lookup per request, but tokens cannot be revoked before they expire, which matters when an admin deactivates a user.
- **Other free hosting options:**
  - **Render (free web service) + Neon or Supabase (free PostgreSQL):** simple Git-based deploys of Docker images, but free services sleep when idle, which stops IMAP polling and the AI worker.
  - **Google Cloud Run:** generous free tier for containers, but scales to zero by default (same problem for the worker) and needs a separate database.
  - **Koyeb / Railway:** easy container hosting, but their free offers are limited or trial-based.
- **Separate repositories for client and server instead of a monorepo:** independent release cycles, but shared schemas would have to be published as a package or copied, and a change spanning both sides needs coordinated pull requests.
- **Redis + BullMQ instead of pg-boss:** higher throughput and a richer feature set, but one more service to run; MVP volume does not need it.
- **Retrieval (embeddings + pgvector) instead of putting the whole handbook in the prompt:** scales to a large knowledge base, but adds indexing and retrieval quality problems to solve before the handbook is big enough to need it.
- **Self-hosted open-source model instead of the Claude API:** keeps all data on our servers, but needs GPU hardware and gives lower response quality for the MVP.

## Decision Rationale
**Why did we select these technologies over the alternatives?**

- **Fewest services that meet the requirements:** PostgreSQL serves as database, job queue, and knowledge-base store, so the MVP runs with an app, a worker, and a database.
- **One repository, one contract:** the monorepo lets client and server share the same validation schemas and types, so a change to a contract is made once, in one pull request, and checked by one CI pipeline.
- **Familiar tools:** React, Vite, and TypeScript match the team's existing experience, which lowers the learning curve.
- **Own application, portable hosting:** Docker keeps the application independent of any one cloud provider; the external dependencies are the cloud host, the email provider, and the AI API.
- **Always-on free hosting:** a free VM runs the worker and database continuously, unlike free platform tiers that sleep when idle.
- **Revocable sessions:** database sessions let an admin cut off a user's access immediately, which fits admin-only user management (M8).
- **Testable end to end:** Mailpit plus Playwright can exercise the whole flow locally, from an email arriving to an AI reply and an agent action.

## Trade-offs
**What compromises are we accepting in terms of performance, cost, complexity, scalability, or maintainability?**

- **IMAP polling adds latency:** new tickets appear after the polling interval (for example, up to one minute), not instantly.
- **Whole handbook in the prompt:** simple and accurate for a small handbook, but cost and prompt size grow with it; retrieval will be needed if the handbook grows large.
- **Single free VM:** no cost and easy to operate, but no high availability, and we manage OS updates, backups, and TLS ourselves.
- **Session lookup per request:** database sessions add a query to each authenticated request; negligible at MVP scale.
- **External AI API:** the best response quality, but ticket content leaves our infrastructure, and each AI call has a usage cost.

## Risks and Limitations
**What technical challenges, dependencies, or limitations should we anticipate?**

- **Data privacy:** student emails are sent to the AI provider; this needs to be checked against institutional policy and applicable regulations before launch.
- **AI accuracy:** misclassification or wrong answers; mitigated by agent review of suggested replies, and by escalation if S3–S5 are adopted.
- **AI availability:** if the AI API is down or slow, jobs are retried and tickets stay visible to agents without AI outputs.
- **Email parsing:** replies, quoted text, signatures, attachments, and threading (matching a reply to its ticket) are often harder than expected.
- **Email deliverability:** the email provider signs messages, but the sending domain still needs SPF, DKIM, and DMARC DNS records.
- **Free-tier changes:** cloud and email providers regularly change or remove free plans (for example, SendGrid replaced its permanent free plan with a time-limited trial in 2025 — verify current terms); keep the setup portable so we can move.
- **Database backups:** PostgreSQL on a free VM has no managed backups; schedule dumps to external storage.
- **Open product questions:** the _CONFLICTING_ items in the [MVP](./2_mvp.md), especially whether students have accounts and whether AI replies are sent automatically, can change the authentication design and the email flow.
