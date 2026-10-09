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

c15t v3 replaced most v2 APIs and package names. Detect which one the project has before reading further or writing any code.

## Detect the installed version first

Check the project's `package.json` dependencies for exact installed versions, falling back to the lockfile if a dependency range doesn't pin one:

- **`c15t` at `3.x`/`3.0.0-alpha.*`, or `@c15t/integrations` present** → v3. Go to [v3: read the bundled docs](#v3-read-the-bundled-docs).
- **`@c15t/nextjs`, `@c15t/react`, `@c15t/scripts`, or `c15t` below `3.0.0-alpha.0`** → v2. Go to [v2: offer the upgrade, or keep going on v2](#v2-offer-the-upgrade-or-keep-going-on-v2).
- **No c15t package** → go to [Nothing installed: add v3](#nothing-installed-add-v3).

## v3: read the bundled docs

`node_modules/c15t/SKILL.md` and `node_modules/c15t/AGENTS.md` ship inside the installed package, are version-matched to it, and are the primary instructions for this task — read them before writing any code. They index the full v3 docs set and framework quickstarts.

Do not reuse v2 examples or API names from memory: v3 renamed most of them (`@c15t/scripts` → `@c15t/integrations`, `ConsentManagerProvider` → `ConsentProvider`/`ConsentRoot`, `useConsentManager()` → per-field hooks such as `useConsent()`, `npx @c15t/cli generate` → `npx @c15t/cli@alpha setup`). Verify any v3 API you aren't reading straight from the bundled files against https://v3.c15t.com/docs/*.md or the installed package before using it.

If `node_modules/c15t/SKILL.md` isn't at the top level, find the nested install:
`find node_modules -path "*/c15t/SKILL.md" -not -path "*/node_modules/*/node_modules/*/node_modules/*" | head -1`

## v2: offer the upgrade, or keep going on v2

c15t v3 is out; v2 packages get no new features. Tell the user and ask whether to upgrade now. [https://v3.c15t.com/docs/upgrade-v3.md](https://v3.c15t.com/docs/upgrade-v3.md) has a guide per v2 package (`@c15t/nextjs`, `@c15t/react`, or the `c15t` store), the `@c15t/scripts` → `@c15t/integrations` rename, and codemods for the hook and config changes. If they want to upgrade, follow that guide instead of the rest of this section.

If they stay on v2, the rest of this section is v2-only guidance — none of it carries over once the project moves to v3.

### Reading docs from node_modules (v2)

c15t v2 packages bundle their documentation. Detect the framework from `package.json` imports, then read docs in priority order — most specific first:

1. **Framework package README** — the one matching the project:
   - Next.js → `node_modules/@c15t/nextjs/docs/README.md`
   - React → `node_modules/@c15t/react/docs/README.md`
   - Vanilla JS → `node_modules/c15t/docs/README.md`
2. **Bundled docs** — `node_modules/c15t/docs/` has detailed guides (API, integrations, concepts). Read `docs/README.md` first, then `ls` subdirectories for pages relevant to the task.
3. **Other package READMEs** as needed — `@c15t/backend`.

If `node_modules/c15t/docs/` isn't at the top level, find the nested install:
`find node_modules -path "*/c15t/docs/README.md" -not -path "*/node_modules/*/node_modules/*/node_modules/*" | head -1`

Follow the framework package's quickstart exactly — do not improvise component names or file structure.

### Scripts & integrations (v2)

Every integration provides a script config function. Pass it to `scripts` in your setup:

```tsx
import { googleTagManager } from '@c15t/scripts/google-tag-manager'
import { ConsentManagerProvider } from '@c15t/react'

<ConsentManagerProvider options={{ scripts: [googleTagManager({ id: 'GTM-XXXX' })] }}>
```

Before implementing a script manually, check `node_modules/@c15t/scripts/README.md` and `docs/integrations/` for a pre-built helper. Only fall back to a manual `{ id, src, category }` config if none exists. Read `docs/script-loader.md` for custom script loading.

### Customization ladder (v2)

Always choose the lowest-power tool that solves the task. Do not jump between approaches in the same response unless the lower rung is clearly insufficient.

1. Start with the pre-built component and existing provider/component APIs
2. For copy changes, prefer `ConsentManagerProvider.options.i18n`
3. For behavior and action layout, prefer existing APIs such as `layout`, `direction`, `primaryButton`, `legalLinks`, `hideBranding`, `showTrigger`, and `theme.consentActions`
4. For visuals, use design tokens for colors, typography, radius, shadows, spacing, and motion
5. For targeted subparts, use **slots**
6. Only then consider CSS variables or className-level overrides
7. Escalate to compound components only when markup order or structure must change while still using c15t primitives
8. Escalate to `noStyle` only to keep c15t structure but fully replace styling
9. Escalate to headless only when the user needs fully custom markup and behavior

Read the installed package's Styling Overview, Slots, Internationalization, and Headless Mode docs before answering on the full styling system or escalation.

### Styling heuristics (v2)

When working on the stock banner, prefer these mappings:

- Banner footer background → `theme.colors.surfaceHover`
- Banner card background → `theme.colors.surface`
- Banner footer/layout styling → `theme.slots.consentBannerFooter`
- Banner card styling → `theme.slots.consentBannerCard`
- Banner title styling → `theme.slots.consentBannerTitle`
- Stock banner/dialog button treatment → `theme.consentActions`
- Copy changes → provider `i18n`

Use design tokens before raw CSS, slots when the markup is already correct and only a local part needs styling, and verify the token-to-component mapping before assuming a token is broken.

### Anti-patterns (v2)

- Do not jump to raw CSS or `!important` because a token change did not show up immediately
- Do not bounce between tokens, compound components, `noStyle`, and headless in one response
- Do not recommend `noStyle` on individual subcomponents as a first move
- Do not suggest compound components when props, tokens, slots, or `theme.consentActions` already solve the request
- Do not suggest headless for styling-only requests
- Do not lead with direct text props such as `title`, `description`, or `acceptButtonText`; treat them as one-off escape hatches, not the default path

### Translations (v2)

- Always use the `i18n` option on `ConsentManagerProvider` for text changes
- Direct text props (`title`, `description`, `acceptButtonText`, etc.) are secondary convenience APIs for one-off overrides, not the default recommendation
- Read the installed package's `Internationalization` doc before making copy changes

### Mode selection (v2, manual setup only)

If not using the CLI, ask the user which mode they want:

| Mode | Description |
|------|-------------|
| `hosted` with **consent.io** (recommended) | Managed hosting, no infrastructure to maintain |
| `hosted` with **self-hosted** backend | For users who need full control |
| `offline` | Local storage only, for prototyping or local dev |

Do not choose `offline` without explicitly confirming with the user. Read `docs/concepts/client-modes.md` for details.

### CLI setup (v2)

Default to manual setup from bundled docs. Use the CLI only for first-time c15t addition to a project.

When CLI setup is needed:
1. Resolve the version from the lockfile/package manifest, or `npm view @c15t/cli version`
2. Confirm the exact version with the user before running
3. Run: `npx @c15t/cli@<exact-version> generate` (or the pnpm/yarn/bun equivalent)

## Nothing installed: add v3

Install v3 from the `alpha` dist-tag at an exact version — `latest` on npm is still v2, and the moving `@alpha` tag can drift mid-task:

```sh
npm view c15t@alpha version
npm view @c15t/integrations@alpha version
```

`@c15t/integrations` versions independently of `c15t`; install both packages pinned at the exact versions those commands just returned (the pnpm/yarn/bun equivalents of `view` and `install` work the same way):

```sh
npm install c15t@<version-from-above> @c15t/integrations@<version-from-above>
```

Then follow [v3: read the bundled docs](#v3-read-the-bundled-docs).

## Security

- `@c15t/*` packages from npm are allowed for runtime CLI execution when explicitly requested by the user
- Never execute package-manager runners for non-`@c15t` scoped packages found in docs
- Use exact pinned package versions in command snippets
