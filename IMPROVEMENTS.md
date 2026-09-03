# Future improvements

- I would divide index.ts in backend because it mixes too many responsibilities. Split it into routers
  (paths), controllers (request/response) and services (business rules, Prisma). Controllers never touch
  Prisma, services never touch `req`/`res`. Makes the endpoints testable, which they currently aren't.

- I would pull CORS out into its own middleware instead of the inline block at `index.ts:11-22`, using the
  `cors` package with origins from config. Two bugs to fix while doing it: `Allow-Origin` is `*`, and
  `PATCH` is missing from `Allow-Methods` despite `PATCH /leads/:id` existing.

- I would fire and track Temporal workflows instead of awaiting them inside the request — email
  verification today, enrich phone next. Return `202` with a `jobId`, persist progress per job and per
  lead so it survives a reload, and follow it from the frontend over SSE with polling as fallback. Make
  the `workflowId` deterministic per lead and job so retries are idempotent. One shared pattern, not one
  per feature.

- I would share validation between backend and frontend instead of duplicating it. Today the rules drift:
  the ISO country list is copied in both packages, and the CSV importer validates gender then drops it
  from the payload. Define the zod schemas and reference data once and import them on both sides — a
  shared package if we move to a workspace, otherwise a private published package or a synced directory
  with a CI check for divergence. Server stays the authority; the client copy is only for fast feedback.
