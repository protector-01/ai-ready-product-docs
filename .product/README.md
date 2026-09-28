# ai-ready-product-docs

**`.product/` — Product Documentation Pattern**

_A versioned set of documents that gives humans and AI agents a common ground: what we build, why, for whom, with what, and in which order._

This folder is a **reusable pattern**. To make it realistic, it is filled in with a real-world scenario: a living application, an **AI-assisted student support ticketing system**, that is defined, built, and evolves over time. Copy the structure to start a new product; keep the conventions to make it readable by both people and AI assistants.

---

## Folder Structure

```
.product/
├── README.md                    ← you are here: purpose, conventions, changelog
├── 1_project-scope.md           ← WHY and WHAT, for the product as a whole
├── 2_mvp.md                     ← WHAT, for the first release
├── 3_tech-stack.md              ← WITH WHAT: technologies and architecture
├── 4_implementation-plan.md     ← IN WHICH ORDER: phases, tasks, testing, delivery
└── assets/
    ├── INSTRUCTIONS.md          ← conventions for humans and AI agents
    └── SHARED_FOLDER_README.md  ← README to copy into the code's shared/ folder
```

## The Documents

Each document builds on the previous one. Read and write them in order.

| # | Document | Answers | Level | Builds on |
| :-: | --- | --- | --- | --- |
| 1 | [Project Scope](./1_project-scope.md) | What problem, for whom, which capabilities? | Product, lasting | — |
| 2 | [MVP](./2_mvp.md) | What exactly ships first, and how do we know it works? | Release | Scope |
| 3 | [Tech Stack](./3_tech-stack.md) | Which technologies and architecture, and why? | Technical decisions | MVP |
| 4 | [Implementation Plan](./4_implementation-plan.md) | In which order do we build, test, and deliver? | Roadmap | MVP, Tech Stack |

Every document follows the same layout:

- An _italic_ line under the title stating the document's purpose.
- `##` sections, each opened by a **bold guiding question** the section answers.

## Guiding Principles

- **Balance over completeness.** A document settles the decisions that would otherwise block the team. Everything else is decided in individual project tickets.
- **Each level has its own scope.** The Project Scope describes the product regardless of release; the MVP holds release-specific rules and exclusions. Both may have an _Out of Scope_ section: one is lasting, the other is deferred.
- **Decide what affects the whole team; leave the rest to the team.** The Tech Stack records choices everyone depends on, such as technologies or the top-level repository structure. How code is organized inside one part of the system is the development team's call.
- **Features are prioritized.** **MUST** features (`M1`, `M2`, …) are required for the release; **SHOULD** features (`S1`, `S2`, …) are desirable and may be deferred. Other documents refer to features by these IDs.
- **Conflicts are made visible, not hidden.** When documents disagree, the disagreement is listed in a temporary _CONFLICTING_ section until someone decides.
- **Links, not names.** Documents refer to each other with relative Markdown links (e.g. `[MVP](./2_mvp.md)`), never bare file names, so references survive renames and wording changes.
- **Shared vocabulary.** Terms are defined once and used consistently, e.g. "user" means a student in this project.

## Document Metadata

Documents that go through review start with a front-matter block:

```yaml
---
project: ai-ready-product-docs               # repository this document belongs to
revision: 1                                  # incremented on each published change
revision_last_date: 09/28/26                 # date of the last revision
approved: no                                 # yes | no — validated by the stakeholders
approved_acceptance: no existing conflict    # condition or reason for the approval status
ready_for_work: yes                          # yes | no — the team can start working from it
ready_for_work_acceptance: partially usable  # condition or reason for the readiness status
---
```

A document can be **ready for work** before it is **approved**: the team starts on what is stable while open points are still being decided.

## Conventions

- **Task status markers** in the Implementation Plan: `[ ]` to do · `[x]` done · `[~]` partially done · `[-]` removed or canceled. See [INSTRUCTIONS](./assets/INSTRUCTIONS.md#task-status-markers) for when and how to use them.
- **Drafts live outside this folder.** Work in progress is prepared elsewhere (e.g. a `.tmp/` folder) and copied here once agreed. Only this folder is the reference, and only its links must work.
- **Assets** are supporting files: conventions for contributors, and templates meant to be copied into the code repository.

## Working with AI Agents

1. Read this README first, then the documents in order (1 → 4).
2. Follow [INSTRUCTIONS](./assets/INSTRUCTIONS.md), especially the rules for marking tasks as done or partially done.
3. When asked to work on one document, do not modify the others unless asked.
4. Do not resolve _CONFLICTING_ items or open questions yourself: propose options and let a human decide.
5. After any change, add an entry to the [Changelog](#changelog).

---

## Changelog

**How has this folder changed over time?**

The folder is versioned as a whole with `MAJOR.MINOR.PATCH`. Each entry lists changes under these categories:

| Category | Use for | Version bump |
| --- | --- | --- |
| **Breaking** | A change that invalidates work already done or planned (e.g. removing a MUST feature, changing a role's permissions, changing the tech stack) | MAJOR |
| **Added** | New documents, sections, features, or tasks | MINOR |
| **Changed** | Updated content that does not invalidate existing work | MINOR |
| **Removed** | Content taken out without impact on work done | MINOR |
| **Fixed** | Typos, broken links, formatting | PATCH |

Versions below `1.0.0` are pre-approval: breaking changes are still expected.

### 0.1.0 — [DATE_UNDEFINED]

**Added**
- Project Scope: problem, solution, features, target audience, success metrics, lasting out-of-scope.
- MVP: MUST features (M1–M9), SHOULD features (S1–S9), _CONFLICTING_ review section, functional specifications (categories, lifecycle, roles and permissions), acceptance criteria, validation strategy.
- Tech Stack: requirements, architecture, monorepo (`client/`, `server/`, `shared/`), technology choices, alternatives, rationale, trade-offs, risks.
- Implementation Plan: phases 0–8, task breakdown with status legend, dependencies, priorities, risks and spikes, testing strategy, definition of done, delivery.
- `assets/INSTRUCTIONS.md`: task status markers and rules for AI agents.
- `assets/SHARED_FOLDER_README.md`: README for the code's `shared/` folder.
- This README.
- `project` metadata field, set to the repository name `ai-ready-product-docs`.
