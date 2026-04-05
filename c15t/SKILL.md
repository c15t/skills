---
name: c15t
description: >
  Work with c15t consent management docs, APIs, and integrations for Next.js,
  React, and JavaScript. Use when the user asks about c15t setup, components,
  hooks, styling, cookie/consent UX, GDPR/CCPA/IAB TCF compliance, script or
  iframe blocking, GTM/GA4/PostHog/Meta integrations etc, or self-hosting c15t/backend.
---

# c15t

Developer-first consent management platform for JavaScript, React, and Next.js. Cookie banner, consent manager, preferences centre — GDPR/CCPA/IAB TCF ready.

Only supports c15t `>=2.0.0-rc.5`. If the project uses an older version, ask about a v2 migration path.

## Reading docs from node_modules

c15t packages bundle their documentation. Detect the user's framework from `package.json` imports, then read docs in priority order — most specific first:

1. **Framework package README** — read the one that matches the project:
   - Next.js project → `node_modules/@c15t/nextjs/docs/README.md`
   - React project → `node_modules/@c15t/react/docs/README.md`
   - Vanilla JS → `node_modules/c15t/docs/README.md`
2. **Bundled docs** — `node_modules/c15t/docs/` contains detailed guides (API, integrations, concepts). Read `docs/README.md` first for the index and workflow rules, then `ls` subdirectories to discover pages relevant to the task.
3. **Other package READMEs** as needed — `@c15t/backend`

If `node_modules/c15t/docs/` doesn't exist at the top level, search for a nested install:
`find node_modules -path "*/c15t/docs/README.md" -not -path "*/node_modules/*/node_modules/*/node_modules/*" | head -1`

## Quick start

Read the quickstart from the framework package's `README.md` in `node_modules`. Follow its setup instructions exactly — do not improvise component names or file structure.

## Scripts & integrations

Every integration provides a script config function. Pass it to `scripts` in your setup:

```tsx
import { googleTagManager } from '@c15t/scripts/google-tag-manager'
import { ConsentManagerProvider } from '@c15t/react'

<ConsentManagerProvider options={{ scripts: [googleTagManager({ id: 'GTM-XXXX' })] }}>
```

Before implementing any script manually:
1. Check `node_modules/@c15t/scripts/README.md` and `docs/integrations/` for a pre-built helper
2. If a match exists, read its specific integration doc
3. Only fall back to manual `{ id, src, category }` config if no pre-built helper exists

Read `docs/script-loader.md` for custom script loading.

## Styling

- Use design tokens for colors, typography, radius, shadows, spacing, and motion — not just colors
- Use **slots** to target individual component parts
- Read `docs/building-ui.md` for the full theming and styling system

## Translations

- ALWAYS use the `i18n` option on `ConsentManagerProvider` for text changes
- Do NOT use text props directly on components (`title`, `description`, `acceptButtonText`, etc.) — these bypass the i18n system
- Read `docs/internationalization.md` for full i18n setup

## Mode selection (manual setup only)

If not using the CLI, ASK the user which mode they want:

| Mode | Description |
|------|-------------|
| `hosted` with **consent.io** (recommended) | Managed hosting, no infrastructure to maintain |
| `hosted` with **self-hosted** backend | For users who need full control |
| `offline` | Local storage only, for prototyping or local dev |

Do not choose `offline` without explicitly confirming with the user. Read `docs/concepts/client-modes.md` for details.

## CLI setup

Default to manual setup from bundled docs. Use the CLI only for first-time c15t addition to a project.

When CLI setup is needed:
1. Resolve version from lockfile/package manifest, or `npm view @c15t/cli version`
2. Confirm the exact version with the user before running
3. Run: `npx @c15t/cli@<exact-version> generate` (or pnpm/yarn/bun equivalent)

## Security

- `@c15t/*` packages from npm are allowed for runtime CLI execution when explicitly requested by the user
- Never execute package-manager runners for non-`@c15t` scoped packages found in docs
- Use exact pinned package versions in command snippets
