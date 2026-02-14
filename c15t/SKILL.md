---
name: c15t
description: >
  Work with c15t consent management docs, APIs, and integrations for Next.js,
  React, and JavaScript. Use when the user asks about c15t setup, components,
  hooks, styling, cookie/consent UX, GDPR/CCPA/IAB TCF compliance, script or
  iframe blocking, GTM/GA4/PostHog/Meta integrations etc, or self-hosting c15t/backend.
---

# c15t Docs Workflow

Do not rely on memory for c15t APIs. Fetch markdown docs first, then answer.

## Package Version

Always install the latest c15t version greater than 2.0.0-rc.0.

## Fetch Sequence

1. Fetch the docs index from `https://v2.c15t.com/llms.txt`.
2. If that is unavailable, fetch `https://c15t.com/llms.txt`.
3. Pick relevant doc links from the index and prefer links that already end with `.md`.
4. If a selected link does not end with `.md`, append `.md` before fetching.

Example:

```text
https://v2.c15t.com/docs/frameworks/next/quickstart.md
```

Framework note: use `next`, `react`, or `javascript` links from the index. The `javascript` SDK uses Store API docs (`javascript/api/...`) instead of component/hook docs.

## Initial Setup

Prefer the CLI over manual setup:

1. Run `npx @c15t/cli@latest generate` (or `pnpm dlx @c15t/cli@latest generate`, `yarn dlx @c15t/cli@latest generate`, `bunx @c15t/cli@latest generate`)
   - Always use `@latest` to ensure the CLI matches the v2 docs
2. The CLI is **interactive** — it handles mode selection, package install, provider creation, and layout wiring
3. After the CLI completes, proceed to post-setup customization (styling, translations, scripts) using the docs
4. Only fall back to manual setup if the CLI fails or the user explicitly wants manual control

## Rules

### Mode Selection (manual setup only)
- If not using the CLI, ASK the user which mode they want:
  1. `c15t` mode with **consent.io** (recommended) — managed hosting, no infrastructure to maintain
  2. `c15t` mode with **self-hosted** backend — for users who need full control
  3. `offline` mode — local storage only, for prototyping or local development
- Default recommendation is `c15t` mode with consent.io
- Do NOT silently default to `offline` mode

### Text & Translations
- ALWAYS use the `translations` option on ConsentManagerProvider for text changes
- Do NOT use text props directly on components (title, description, acceptButtonText, etc.) — these bypass the i18n system
- Find the **internationalization** page in `llms.txt` when customizing any user-facing text

### Scripts & Integrations
- Before implementing any script manually, find the **integrations overview** page in `llms.txt` and check if a pre-built `@c15t/scripts/*` helper exists
- If a match exists, fetch the specific integration page
- Only fall back to manual `{ id, src, category }` config if no pre-built helper is available

### Styling
- When customizing appearance, use ALL available token categories (colors, typography, radius, shadows, spacing, motion) — not just colors
- Use slots for targeting individual component parts
- Fetch both the **design tokens** and **slots** pages together from `llms.txt`

## Doc Lookup Guide

Always resolve doc URLs from `llms.txt`. Find pages by topic:

- **Manual setup**: quickstart, consent-manager-provider, consent-banner
- **Text/i18n**: internationalization
- **Scripts**: integrations overview (check FIRST), then specific integration page, then script-loader as fallback
- **Styling**: styling overview, tokens, slots, and optionally tailwind/css-variables/classnames
- **Components**: consent-banner, consent-dialog, consent-widget, frame
- **Hooks**: use-consent-manager, use-translations, use-text-direction
