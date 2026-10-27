# GitHub → Cloudflare deployment

HOOF is live at https://hoof-game.a2040404.workers.dev with the `hoof-leaderboard` D1 database connected. Git-triggered builds previously published from the portfolio repo; reconnect Workers Builds to this repository before expecting a push to deploy.

Use these project settings:

| Setting | Value |
| --- | --- |
| GitHub repository | `clement55688/clement-arcade` |
| Production branch | `main` |
| Root directory | repository root |
| Worker name | `hoof-game` |
| Build command | `npm run check` |
| Deploy command | `npm run deploy:cloudflare` |
| Node version | `24` (also declared in `.node-version`) |
| Package manager | `pnpm@11.19.0` |

The arcade now lives at the root of this repository. Point Cloudflare Workers
Builds here, with production branch `main` and no subdirectory. Worker name
stays `hoof-game`, so https://hoof-game.a2040404.workers.dev does not change.
The previous portfolio connection (`clement-portfolio`, branches
`codex/hoof-game` then `arcade/hoof-game`, folders `horse-game/` then
`clement-arcade/`) will not publish this repo. The managed deployment token is
not stored in source control. Local deploys need `npx wrangler login` or
`CLOUDFLARE_API_TOKEN`. Git-triggered builds need the Git connection moved to
this repository; they do not need a new token.

The database UUID and account ID in `wrangler.jsonc` were read from the signed-in Cloudflare dashboard. These identifiers are not secrets. The deployment token is managed by Cloudflare, not stored in source control. Preview builds are disabled so they cannot modify the production leaderboard.

The deployment command applies the SQL migrations in `drizzle/` before publishing the Worker. It refuses to deploy without a configured database, so the leaderboard cannot silently launch without storage. The Cloudflare build must pass the game and API checks before deployment. Turn off automatic deployments from non-production branches unless separate preview resources are configured.

Each push to the production branch will then update the Worker URL supplied by Cloudflare. A custom domain can be attached afterward. The current Sites URL remains available independently; the Sites-managed database is separate from the Cloudflare account's database. Existing scores need an explicit migration if they should appear on the new host.

For local development, the existing `npm run dev` command still uses the local SQLite database. To validate or deploy through Wrangler, install game dependencies first with `npm install` (or the project's lockfile package manager).

References:

- https://developers.cloudflare.com/workers/ci-cd/builds/
- https://developers.cloudflare.com/d1/reference/migrations/
