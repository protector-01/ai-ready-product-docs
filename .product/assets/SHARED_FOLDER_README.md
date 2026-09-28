# shared

Code used by **both** the `client/` and the `server/` workspaces.

## Why this folder exists

The client and the server must agree on the shape of the data they exchange: what a ticket looks like, which fields a form requires, which roles exist. If each side defines these on its own, they drift apart, and the mismatch only shows up at runtime.

`shared/` is the single place where these contracts are defined. Both sides import them, so:

- A contract changes in one place, in one pull request.
- The same validation rules run in the browser (instant feedback in forms) and on the API (protection against invalid requests).
- TypeScript reports a mismatch between client and server at build time, before it reaches users.

## What belongs here

Only things that **both** sides need and that describe **what** the data is, not how it is handled:

- **Validation schemas (Zod):** request bodies and form inputs, such as creating a user or sending a reply.
- **Types inferred from those schemas,** and the types of API responses.
- **Shared constants and enumerations:** roles (`admin`, `agent`), ticket statuses, ticket categories.
- **Small pure helpers** that both sides need and that have no side effects, such as formatting a ticket reference.

## What does not belong here

- **Code used by only one side:** keep it in `client/` or `server/`. Move it here only when the second side actually needs it.
- **UI code:** React components, hooks, and styles.
- **Server-only code:** database access, email, AI calls, authentication logic.
- **Secrets and environment configuration.**
- **Anything with heavy dependencies:** `shared/` is bundled into the browser, so it must stay small and free of Node-only packages.

When in doubt, ask: _"Would the client and the server break or disagree if this were defined twice?"_ If yes, it belongs here.
