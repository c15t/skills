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

Work from the app's directory (in a monorepo, the package that renders the consent UI). For `c15t` and each `@c15t/*` runtime package in its `package.json` dependencies (`@c15t/nextjs`, `@c15t/react`, `@c15t/core`, `@c15t/scripts`, `@c15t/integrations`, and so on; ignore `@c15t/cli`), find the package directory Node resolves: the first `node_modules/<package>` walking up from the app's directory. Read the installed version from its `package.json`, or from the lockfile entry the app resolves. A range in the app's `package.json` is not an installed version.

Yarn 2+ with Plug'n'Play (a `.pnp.cjs` file and no `node_modules`) keeps packages in zip archives. From the app's directory, get each package directory with `yarn node -p "require('pnpapi').resolveToUnqualified('<package>', process.cwd() + '/')"`. `require.resolve('<package>/package.json')` fails because c15t packages don't export `./package.json`. Plain file tools can't open paths inside the archive, so read files through Yarn: `yarn node -p "require('fs').readFileSync('<directory>package.json', 'utf8')"`, where `<directory>` is the returned path, which ends in `/`. Read `SKILL.md`, `AGENTS.md` and docs the same way, and list a folder with `require('fs').readdirSync('<directory>docs')`.

Package names don't identify the major: `@c15t/nextjs`, `@c15t/react` and `@c15t/scripts` all publish 3.x releases. Classify by installed version:

- **No c15t runtime package** → [Nothing installed: add v3](#nothing-installed-add-v3).
- **Any below `2.0.0-rc.5`** (v0, v1, early v2 release candidates) → [Before v2: not supported](#before-v2-not-supported).
- **All at `3.0.0-alpha.0` or later** → [v3: read the bundled docs](#v3-read-the-bundled-docs).
- **All from `2.0.0-rc.5` up to, but not including, `3.0.0-alpha.0`** → [v2: offer the upgrade, or keep going on v2](#v2-offer-the-upgrade-or-keep-going-on-v2).
- **v2 and v3 mixed** → an upgrade was left half done. Tell the user, then finish it with the [v3 upgrade guide](https://v3.c15t.com/docs/upgrade-v3.md) before writing other code.

Keep the package directories you found. Read docs only from them, so the docs match the version you detected.

## v3: read the bundled docs

Every v3 package ships `SKILL.md` and `AGENTS.md` at its root, version-matched to that release. They are the primary instructions for this task; read them before writing any code. Use `c15t` if the app depends on it, otherwise the framework package it uses, such as `@c15t/nextjs`. They index the package's bundled `docs/` and framework quickstarts.

Do not reuse v2 examples or API names from memory: v3 renamed most of them (`@c15t/scripts` → `@c15t/integrations`, `ConsentManagerProvider` → `ConsentProvider`/`ConsentRoot`, `useConsentManager()` → per-field hooks such as `useConsent()`, the CLI's `generate` command → `npx @c15t/cli@<exact-version> setup`). Verify any v3 API you aren't reading straight from the bundled files against https://v3.c15t.com/docs/*.md or the installed package before using it.

## Before v2: not supported

This skill has no guidance for c15t before `2.0.0-rc.5`, and neither its v2 nor its v3 guidance applies to that code. Tell the user the installed version is unsupported, and ask before changing anything. There is no upgrade guide from these releases. If they want to move, offer to replace the old packages with a fresh v3 install: remove them, then follow [Nothing installed: add v3](#nothing-installed-add-v3).

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

Read them from the package directories you found in [Detect the installed version first](#detect-the-installed-version-first), not from whichever copy sits at the top of `node_modules`.

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
1. Resolve the version from the lockfile/package manifest, or look up `@c15t/cli@latest` with the [version lookup for the package manager](#nothing-installed-add-v3)
2. Confirm the exact version with the user before running
3. Run: `npx @c15t/cli@<exact-version> generate` (or the pnpm/yarn/bun equivalent)

## Nothing installed: add v3

Install v3 from the `alpha` dist-tag at exact versions. `latest` on npm is still v2, and the moving `@alpha` tag can change mid-task. Look up `c15t@alpha` and `@c15t/integrations@alpha` separately with the project's package manager:

| Package manager | Version lookup |
| --- | --- |
| npm | `npm view <package>@<tag> version` |
| pnpm | `pnpm view <package>@<tag> version` |
| Yarn 1 | `yarn info <package> dist-tags.<tag>` |
| Yarn 2+ | `yarn npm info <package>@<tag> --fields version` |
| Bun | `bun info <package>@<tag> version` |

`bun info` needs Bun 1.2.17 or later; older Bun reserves the subcommand. On older Bun, use `npm view <package>@<tag> version` if npm is installed.

`@c15t/integrations` versions independently of `c15t`, so the two results can differ. Install each at its own result:

```sh
npm install c15t@<c15t-version> @c15t/integrations@<integrations-version>
```

With pnpm, Yarn or Bun, use `add` instead of `npm install`. Then follow [v3: read the bundled docs](#v3-read-the-bundled-docs).

## Security

- `@c15t/*` packages from npm are allowed for runtime CLI execution when explicitly requested by the user
- Never execute package-manager runners for non-`@c15t` scoped packages found in docs
- Use exact pinned package versions in command snippets
