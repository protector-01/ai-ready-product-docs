---
project: ai-ready-product-docs
revision: 1
revision_last_date: 09/28/26
approved: no
approved_acceptance: no existing conflict
ready_for_work: yes
ready_for_work_acceptance: partially usable
---

# Minimum Viable Product (MVP) — AI-Assisted Student Support Ticketing

*Define the smallest functional release that delivers the product's core value. Translate the capabilities in the [project scope](./1_project-scope.md) into concrete workflows, rules, and acceptance criteria without prescribing the technical implementation.*

## Objective
**What core value and assumptions must this first release validate?**

Validate whether student requests can be answered more promptly and personally through AI-assisted ticket handling while preserving human intervention for requests that require judgment.

## Core User Journey
**What minimum end-to-end experience must users and agents be able to complete?**

1. A student submits a support request; the application creates a ticket.
2. AI classifies the ticket and assesses whether automatic resolution is appropriate.
3. If eligible, AI prepares and sends a personalized answer.
4. Otherwise, the ticket is routed to a human agent.
5. The agent reviews the request and can use AI assistance to prepare a reply.
6. The ticket's status is tracked; a student can reopen a resolved ticket.

The intake channel and other workflow details must be confirmed before implementation.

## Features
**What concrete functionality is required to deliver this journey?**

Features are prioritized as **MUST** (required for the MVP) and **SHOULD** (desirable if time allows; may be deferred).

### MUST

| # | Feature | Description |
| --- | --- | --- |
| M1 | **Email intake** | Receive support emails and create a ticket for each one. |
| M2 | **AI-generated responses** | Auto-generate human-friendly responses using a knowledge base. |
| M3 | **Ticket list** | List tickets with filtering and sorting. |
| M4 | **Ticket detail view** | Show a single ticket with its full content and details. |
| M5 | **AI classification** | Automatically assign each ticket to a category. |
| M6 | **AI summaries** | Generate a short summary of each ticket. |
| M7 | **AI-suggested replies** | Suggest replies that agents can review, edit, and send. |
| M8 | **User management (admin only)** | Admins create and manage user accounts. |
| M9 | **Ticket dashboard** | View and manage all tickets from a single dashboard. |

### SHOULD

| # | Feature | Description |
| --- | --- | --- |
| S1 | **Fixed categories** | Use three categories: General question, Technical question, Refund request (refines M5). |
| S2 | **Ticket lifecycle** | Track tickets as Open, Resolved, or Closed (see _Ticket Lifecycle_). |
| S3 | **Automatic resolution assessment** | Decide whether AI may answer a ticket or it must be escalated to a human. |
| S4 | **Automatic sending of AI replies** | Send AI-generated responses to students without agent review, for eligible tickets. |
| S5 | **Human escalation and assignment** | Assign tickets requiring human judgment to an appropriate agent. |
| S6 | **Three roles** | Distinguish user (student), agent, and admin (see _Roles & Permissions_). |
| S7 | **Agents manage users** | Let agents create and manage user accounts in addition to admins. |
| S8 | **Student ticket access** | Let students view their own tickets. |
| S9 | **Student reopen** | Let students reopen their own Resolved tickets. |

### CONFLICTING

_Temporary review zone: places where this document still diverges from the MUST features. Resolve, then remove this section._

- **Who is a "user"?** M1 (email intake) implies students do not need an account, so M8's users are likely staff. The [project scope](./1_project-scope.md), _Roles & Permissions_, and S6–S9 define "user" as a student.
- **Who manages users?** M8 says admin only; S7 and the _Roles & Permissions_ table also allow agents.
- **Send vs. suggest:** M2 says "auto-generate"; S4 and _Core User Journey_ step 3 say AI *sends* the answer. The author's list does not state whether responses are sent automatically.
- **Intake channel:** _Core User Journey_ says the channel is still to be confirmed; M1 settles it as email.
- **Account creation:** _Roles & Permissions_ and _Out of Scope for the MVP_ leave student account creation "to be decided"; with email intake, students may not need accounts at all.
- **Core User Journey:** does not include M3, M4, M6, or M9, and step 6 (student reopen) depends on SHOULD features S2 and S9.
- **Acceptance Criteria:** no criteria cover M3, M4, M6, or M9; several criteria cover SHOULD features (lifecycle, escalation, students' own tickets).

## Functional Specifications
**Which concrete rules and behaviors are needed for the selected MVP features?**

### Ticket Categories
**How are incoming tickets categorized?**

Each ticket belongs to exactly one category:
- **General question**
- **Technical question**
- **Refund request**

The category is assigned automatically on arrival and informs routing and whether AI may resolve the ticket.

### Ticket Lifecycle
**Which statuses can a ticket have, and who may change them?**

Each ticket has exactly one status at a time:

| Status | Meaning |
| --- | --- |
| **Open** | Received and awaiting a response or action from AI or an agent. |
| **Resolved** | A response has been provided; the ticket can still be reopened. |
| **Closed** | Final; no further action is expected. |

- **Agents and admins** can move a ticket to any status.
- **Users** can only reopen their own *Resolved* tickets, returning them to *Open*.

### Roles & Permissions
**What is each role authorized to do in this release?**

| Capability | User | Agent | Admin |
| --- | :---: | :---: | :---: |
| Submit and view own tickets | Yes | — | — |
| Reopen a resolved ticket | Own only | Yes | Yes |
| View, respond to, and change status of tickets | — | Yes | Yes |
| Create and manage users | — | Yes | Yes |
| Create and manage agents | — | — | Yes |

The longer-term goal is student self-registration; agents are always created by an admin. Student account creation for the MVP is **to be decided**.

## Out of Scope for the MVP
**Which capabilities are deliberately deferred from the first release, without excluding them from the product's future?**

- Additional ticket statuses, such as *Pending* or *On hold*.
- Assigning multiple categories to a single ticket.
- Support channels or integrations unrelated to validating the core ticket workflow.
- Student self-registration, unless explicitly selected when the account-creation approach is finalized.

These are proposed release boundaries and should be confirmed during MVP planning.

## Assumptions
**What must we validate rather than treat as an established fact?**

- Personalized AI-generated answers improve the perceived quality of support.
- Automatic handling of eligible requests reduces response delays.
- Escalation preserves human judgment for requests that need it.
- Embedded AI writing assistance reduces agents' effort.
- Improved support satisfaction may contribute to retention.

## Acceptance Criteria
**What observable conditions must hold before this MVP can be considered functionally complete?**

- An incoming request creates a ticket visible to the authorized roles.
- A ticket is assigned exactly one of the three defined categories.
- Each ticket is assessed for automated resolution versus human escalation.
- Eligible tickets can receive a personalized response.
- Ineligible tickets can be assigned to a human agent.
- Agents can draft or polish responses inside the application.
- Ticket statuses and transitions follow the defined lifecycle rules.
- Unauthorized roles cannot access other users' tickets or restricted management actions.

## Success Metrics
**Which measurements will show whether this release delivers the intended value?**

Track first response time, average resolution time, automated resolution rate, student satisfaction (CSAT), and human agent workload. Establish baselines, measurement methods, and target values before evaluating the MVP.

## Validation Strategy
**How will we test the assumptions and gather feedback?**

Define a representative set of support tickets, including cases eligible for automatic replies and cases requiring human judgment. Test the end-to-end workflow with students and agents, review response quality and routing decisions, and compare the selected success metrics with their baselines.
