# Repository-specific Copilot instructions

This project is a Next.js (app-router) blog site with markdown content in `public/blogs/`. These notes capture patterns and workflows an AI coding agent should follow to be productive.

**Architecture Overview**
- **Framework**: Next.js 16 app-router under `src/app/` (server components by default). See `src/app/layout.tsx`.
- **Static blog content**: Markdown files live in `public/blogs/{slug}/index.md` with `config.json` metadata. The site dynamically fetches these via client fetches (see `src/lib/load-blog.ts`).
- **GitHub-backed editing**: Editing/publishing is implemented in the client using GitHub REST APIs via `src/lib/github-client.ts` and higher-level helpers in `src/lib/blog-index.ts`.
- **SVG handling**: SVGs are compiled via `@svgr/webpack`; there's a helper script `scripts/gen-svgs-index.js` and an npm script `svg` to regenerate indexes.

**Key Commands / Developer Workflows**
- **Dev server**: `pnpm dev` or `npm run dev` runs `next dev --turbopack -p 2025`. Default port is `2025`.
- **Build / Start**: `pnpm build` / `pnpm start` (scripts call `next build` / `next start`).
- **Format**: `pnpm format` runs Prettier for relevant files.
- **Regenerate SVG index**: `pnpm svg` runs `node scripts/gen-svgs-index.js`.

**Project Conventions & Patterns**
- **File layout**: UI code in `src/components/`, layout in `src/layout/`, app router pages in `src/app/`, utilities in `src/lib/`, hooks in `src/hooks/`, and styles in `src/styles/`.
- **Client vs Server**: Files with `'use client'` are client-only (see `src/lib/blog-index.ts`, many components). Default exported async components in `src/app/` are server components. Be conservative when moving code between client/server: DOM and browser API usage must stay in client components.
- **Blog data access**: Read-only rendering fetches from `/blogs/{slug}` (client-side fetch). Any code that writes or updates blog files uses `src/lib/github-client.ts` to call GitHub APIs and expects base64 payloads (see `toBase64Utf8`, `putFile`, `readTextFileFromRepo`).
- **Index updates**: The canonical blog list is `public/blogs/index.json`. Use helpers in `src/lib/blog-index.ts` for consistent formatting and ordering.

**Important Files to Reference**
- `src/app/layout.tsx` — root app layout and metadata.
- `src/lib/load-blog.ts` — how the frontend loads markdown + config from `public/blogs/`.
- `src/lib/github-client.ts` — GitHub REST helper functions, file operations, JWT helper.
- `src/lib/blog-index.ts` — helpers to prepare and upsert `public/blogs/index.json`.
- `next.config.ts` — turbopack & webpack rules; SVG loader configuration.

**Security & Auth notes (discoverable patterns)**
- GitHub operations assume a token or app JWT; `src/lib/github-client.ts` contains helpers to sign an app JWT (`signAppJwt`) using `jsrsasign` and to create installation tokens.
- Client-side code calls GitHub APIs directly; avoid leaking private keys or app secrets into code — tests or local runs should use environment variables not committed files.

**What to avoid changing without explicit verification**
- Changing `public/blogs/*` structure or `public/blogs/index.json` format: many helpers assume the index is an array of items sorted by `date` and consumed by `src/lib/blog-index.ts`.
- Modifying the GitHub API call shapes: `src/lib/github-client.ts` is relied on by several client modules; keep request/response expectations intact.

If anything is unclear or you want the instructions expanded with code snippets for common tasks (e.g., “add a new blog programmatically”, or “create a client-only editor component”), tell me which area to expand. 
