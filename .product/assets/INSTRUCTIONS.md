# Instructions

_Conventions for humans and AI agents working with the product documents in `.product/`._

## Task Status Markers

**How do we record the progress of a task in the [Implementation Plan](../4_implementation-plan.md)?**

| Marker | Meaning | When to use it |
| --- | --- | --- |
| `[ ]` | To do | Not started, or started but nothing is delivered yet. |
| `[x]` | Done | **Everything** the task describes is delivered and meets the [Definition of Done](../4_implementation-plan.md#definition-of-done). |
| `[~]` | Partially done | Part of the task is delivered and usable; the rest is not, and may be deferred. |
| `[-]` | Removed or canceled | The task will not be done in this release. |

### Why "partially done" exists

A task often covers more than one thing. For example, task 3.5 is "filter the list by status and date, and search by sender or subject". If the filters ship but search does not, neither `[x]` nor `[ ]` is true:

- `[x]` would claim search exists. Anyone reading the plan, human or AI, would then treat it as delivered, build on it, or test for it.
- `[ ]` would hide the work that is actually delivered.

Saying "task 3.5 is done, and search becomes a SHOULD" in a conversation does not fix this either: the plan still says one thing and reality another, and an AI assistant reading the plan later has no way to know. `[~]` makes the gap visible **in the document itself**, and the note next to it says where the rest went.

### How to mark a task as partially done

1. Change the marker to `[~]`.
2. Add a short note on the same line: what is delivered, what is not, and where the remainder is tracked.
3. Record the remainder where it will be picked up, usually as a new SHOULD item in the [MVP](../2_mvp.md) or as a new task in a later phase, with a reference back to the original task.

Example:

```markdown
- [~] 3.5 Filter the list by status and date, and search by sender or subject.
  _Done: status and date filters. Not done: search → moved to S10 in the MVP._
```

A task stays `[~]` for the rest of the release. It becomes `[x]` only if the remainder is later delivered as part of the same task.

### How to mark a task as removed or canceled

Change the marker to `[-]` and add a short note with the reason and the decision date. Do not delete the line: the history of what was dropped, and why, is part of the plan.

```markdown
- [-] 6.2 Manage tickets from the dashboard.
  _Canceled 2026-10-15: status changes stay in the ticket detail view._
```

### Rules for AI agents

- **Never mark a task `[x]` unless every part of its description is delivered.** If any part is missing, use `[~]`.
- **Never drop the remainder of a partial task silently.** Always add the note and record the remainder (step 3), or ask the user where it should go.
- **When told "task N is done"**, compare that statement with the task description. If the description covers more than was mentioned, ask whether the rest is done, or propose `[~]` with a note.
- **Treat `[~]` and `[-]` notes as the source of truth** for what was delivered, deferred, or dropped when planning next steps or writing tests.
