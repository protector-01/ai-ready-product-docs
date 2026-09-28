---
project: ai-ready-product-docs
revision: 1
revision_last_date: 09/28/26
approved: no
approved_acceptance: no existing conflict
ready_for_work: yes
ready_for_work_acceptance: partially usable
---

# Implementation Plan — AI-Assisted Student Support Ticketing

_Define how the MVP will be built, tested, and delivered by organizing development into actionable tasks, milestones, and priorities._

> Feature references (M1–M9, S1–S9) point to the [MVP](./2_mvp.md); technology choices come from the [Tech Stack](./3_tech-stack.md). This plan is a roadmap: detailed tasks are refined in project tickets.

## Implementation Strategy
**How will we approach development and organize the work?**

- **Build in dependency order:** each phase builds on what the previous one delivered (e.g. tickets must exist before they can be listed, and be classified before they can be filtered by category).
- **MUST before SHOULD:** all MUST features (M1–M9) are delivered before any SHOULD feature (S1–S9) is started.
- **Thin working slices:** each phase ends with something usable end to end, deployed and tested, rather than finishing one technical layer at a time.
- **Deploy early:** the application is deployed to the cloud host from the first phase, so hosting issues surface early instead of at release time.
- **Try risky parts first:** uncertain areas (email threading, AI quality) are explored with small spikes before the phase that depends on them.

## Prerequisites
**What must be decided or available before development starts?**

- **Product decisions:** the _CONFLICTING_ items in the MVP, at least "who is a user" and "who manages users", since they shape authentication (Phase 1).
- **Accounts and access:** cloud host (Oracle Cloud), email provider (Mailgun or SendGrid), Anthropic API key, a domain name, and a support mailbox reachable over IMAP.
- **Content:** the support handbook for the knowledge base, and a set of real (anonymized) sample tickets for testing and AI evaluation.

## Milestones
**What are the major stages of implementation, and what should each stage deliver?**

| Phase | Goal | Features | Delivers |
| --- | --- | --- | --- |
| **0. Foundations** | Project runs locally and in the cloud | — | Empty app deployed, with CI, database, and local mail server |
| **1. Accounts & access** | Staff can sign in; admins manage users | M8 | Login, roles, user management |
| **2. Ticket intake** | Support emails become tickets | M1 | Tickets created from incoming email |
| **3. Working tickets** | Staff can find, read, and answer tickets | M3, M4 | Ticket list, detail view, email replies |
| **4. AI insights** | Tickets are classified and summarized | M5, M6 | Category and summary on every ticket |
| **5. AI responses** | AI drafts answers from the knowledge base | M2, M7 | Knowledge base, generated responses, suggested replies |
| **6. Dashboard** | One place to view and manage all tickets | M9 | Dashboard |
| **7. MVP release** | MVP live and accepted | — | Production release, acceptance sign-off |
| **8. SHOULD features** | Optional improvements, if time allows | S1–S9 | Selected SHOULD features |

## Task Breakdown
**What concrete development tasks must be completed for each milestone?**

> **Legend:** `[ ]` to do · `[x]` done · `[~]` partially done · `[-]` removed or canceled. See the [instructions](./assets/INSTRUCTIONS.md#task-status-markers) before marking a task `[~]` or `[-]`.

### Phase 0 — Foundations
- [ ] 0.1 Create the monorepo with `client/`, `server/`, and `shared/` workspaces (see the [Tech Stack](./3_tech-stack.md)), with linting and formatting.
- [ ] 0.2 Run the stack locally with Docker Compose: app, worker, PostgreSQL, and Mailpit.
- [ ] 0.3 Set up the database schema and migrations.
- [ ] 0.4 Set up CI: lint, unit tests, and build on each change.
- [ ] 0.5 Provision the cloud host and deploy the empty app with HTTPS.
- [ ] 0.6 Set up the test tooling (unit, API, and end-to-end) with one passing test each.

### Phase 1 — Accounts & access (M8)
- [ ] 1.1 Store users with a role (admin, agent).
- [ ] 1.2 Create the first admin account on setup.
- [ ] 1.3 Log in and log out, with sessions stored in the database.
- [ ] 1.4 Protect pages and API actions by role.
- [ ] 1.5 Admin creates, edits, and deactivates users; deactivation ends active sessions.

### Phase 2 — Ticket intake (M1)
- [ ] 2.1 Spike: read a mailbox over IMAP and match replies to an existing conversation.
- [ ] 2.2 Store tickets and their messages (sender, subject, body, received date, status).
- [ ] 2.3 Poll the support mailbox and create a ticket for each new email.
- [ ] 2.4 Attach a follow-up email from the same sender to its existing ticket instead of creating a new one.
- [ ] 2.5 Ignore duplicates, auto-replies, and bounces.

### Phase 3 — Working tickets (M3, M4)
- [ ] 3.1 Ticket detail view: full conversation and ticket information (M4).
- [ ] 3.2 Agent writes a reply from the detail view; it is sent to the student by email.
- [ ] 3.3 Ticket list with pagination (M3).
- [ ] 3.4 Sort the list (e.g. by date received or last activity).
- [ ] 3.5 Filter the list by status and date, and search by sender or subject.

### Phase 4 — AI insights (M5, M6)
- [ ] 4.1 Run AI jobs in the background, with retries, when a ticket is created.
- [ ] 4.2 Classify each ticket into one category (M5), using the three categories from S1 as the default list.
- [ ] 4.3 Show the category on the list and detail view; add filtering by category to the list.
- [ ] 4.4 Generate a short summary of each ticket (M6) and show it on the detail view.
- [ ] 4.5 Tickets remain usable when AI fails: they show without category or summary, and jobs are retried.

### Phase 5 — AI responses (M2, M7)
- [ ] 5.1 Store the knowledge base (handbook documents) and let admins update it.
- [ ] 5.2 Spike: measure answer quality against the sample tickets with the whole handbook in the prompt.
- [ ] 5.3 Generate a human-friendly response for each new ticket using the knowledge base (M2).
- [ ] 5.4 Agent requests a suggested reply, then edits and sends it (M7).
- [ ] 5.5 Mark AI-generated content clearly for agents.

### Phase 6 — Dashboard (M9)
- [ ] 6.1 Overview of all tickets: counts by status and category, recent activity.
- [ ] 6.2 Manage tickets from the dashboard (e.g. change status, open ticket).

### Phase 7 — MVP release
- [ ] 7.1 Configure the email domain for sending (SPF, DKIM, DMARC).
- [ ] 7.2 Schedule database backups to external storage.
- [ ] 7.3 Run the full end-to-end suite against production-like settings.
- [ ] 7.4 Review the MVP acceptance criteria with stakeholders and sign off.

### Phase 8 — SHOULD features (S1–S9)
Picked in order once all MUST features are released; items marked 🔒 depend on unresolved _CONFLICTING_ decisions in the MVP.
- [ ] 8.1 Ticket lifecycle rules: Open, Resolved, Closed, and allowed transitions (S2).
- [ ] 8.2 Automatic resolution assessment: AI or human (S3).
- [ ] 8.3 Human escalation and assignment to an agent (S5).
- [ ] 8.4 Send eligible AI responses automatically, without agent review (S4). 🔒
- [ ] 8.5 Student role, student access to own tickets, and reopening resolved tickets (S6, S8, S9). 🔒
- [ ] 8.6 Agents manage users (S7). 🔒

## Dependencies
**Which tasks or components depend on others being completed first?**

- **Phase 0 → everything:** nothing can be tested or deployed without the foundations.
- **Phase 1 → Phases 3, 5, 6:** staff pages and admin-only actions need login and roles.
- **Phase 2 → Phases 3–6:** there is nothing to list, view, classify, or answer until tickets exist.
- **4.1 (background jobs) → all AI tasks** in Phases 4 and 5.
- **4.2 (classification) → 4.3 (filter by category), 6.1 (counts by category), 8.2 (assessment).**
- **5.1 (knowledge base) → 5.3, 5.4:** responses must be grounded in the handbook.
- **3.2 (email replies) → 5.4 (send a suggested reply), 8.4 (automatic sending).**
- **8.1 (lifecycle) → 8.3, 8.5:** escalation and reopening rely on defined statuses.

## Priorities
**What should we build first, and in what order?**

1. Phases 0–3: a working ticketing system without AI; it is already usable by agents.
2. Phases 4–5: the AI features that deliver the product's core value.
3. Phase 6: the dashboard, which builds on everything above.
4. Phase 7: release the MVP.
5. Phase 8: SHOULD features, starting with those not blocked by open decisions (🔒).

If time runs short, Phase 6 can be reduced to a read-only overview before cutting any AI feature.

## Risks and Early Spikes
**Which uncertain parts should we explore first, and how?**

- **Email threading and parsing (spike 2.1):** matching replies to tickets and stripping quoted text is often harder than expected.
- **AI answer quality (spike 5.2):** if answers are poor with the whole handbook in the prompt, retrieval may be needed earlier than planned.
- **Free-tier limits (task 0.5):** deploying in Phase 0 checks the cloud host's limits before the app depends on them.
- **Open product decisions:** unresolved _CONFLICTING_ items block the 🔒 tasks; they should be decided before Phase 8.

## Testing Strategy
**How will we verify that each component works as expected?**

- **Unit tests:** business rules such as permissions, email matching, and status changes.
- **API tests:** each endpoint, including role checks (an agent cannot reach admin-only actions).
- **End-to-end tests:** each MUST feature as a user scenario, using the local mail server to send real emails, for example:
  - An email arrives → a ticket appears in the list.
  - An admin creates an agent → the agent can log in.
  - An agent filters the list by category → only matching tickets appear.
  - An agent sends a suggested reply → the student receives the email.
- **AI outputs:** end-to-end tests replace the AI with fixed responses so they are repeatable. AI quality is checked separately by running the sample tickets and comparing categories and answers with expected results.

## Definition of Done
**What conditions must be met before considering a task or milestone complete?**

- **Task:** works as described, has tests at the appropriate level, passes lint and CI, and has been reviewed.
- **Phase:** all its tasks are done, its end-to-end scenarios pass, and it is deployed to the cloud host.
- **MVP:** Phases 0–7 are done and the MVP acceptance criteria are signed off.

## Delivery and Validation
**How will we deploy the MVP and verify that it meets its functional requirements?**

- **Environments:** local (Docker Compose with Mailpit) and production (cloud host). A separate staging environment is optional, given the free-tier budget.
- **Deployment:** each phase is deployed when done, so the release in Phase 7 is a final configuration and verification step, not a first deployment.
- **Validation:** follow the [MVP](./2_mvp.md) _Validation Strategy_: test with the sample tickets and real agents, review response quality, and compare the success metrics with their baselines.
