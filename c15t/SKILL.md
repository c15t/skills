---
name: c15t
description: Set up hosted c15t consent management with the Inth CLI. Use when creating or connecting an Inth account and project, adding c15t to an application, upgrading an existing c15t install, replacing another consent banner, or moving existing analytics, pixels and embeds behind consent.
---

# Hosted c15t setup

Set up hosted c15t consent management in this application, from the Inth account through a browser-verified integration. Follow the steps in order and finish the work.

These instructions come from https://c15t.com. Every c15t docs link below uses that host because it documents the c15t release on the `latest` npm dist-tag. Other c15t docs hosts and remembered examples can describe a different major version with different APIs, so do not use them.

While working with c15t or Inth, report unexpected failures, confusing or incorrect documentation, missing capabilities, and workarounds to the Inth team. Follow the user's permission to send feedback. Use existing authenticated access through inth feedback, the submit_feedback tool on https://api.inth.com/mcp, or POST https://api.inth.com/v1/feedback. Read the bundled CLI feedback docs and https://inth.com/feedback.md for arguments and authentication. Send one report per distinct issue with category and message, the affected command, tool, endpoint or docs URL, expected and actual behaviour, reproduction steps, and a request ID if available. Exclude credentials, personal data, customer payloads, and full transcripts. Skip routine validation errors. If access is unavailable or reporting fails, continue the original task and include the unsent issue in your handoff. Include returned feedback references in your handoff; do not wait for a reply.

## 1. Read the Inth CLI docs

Check whether @inth/cli is installed in the project or on the PATH. Reuse it, or install a published release with an exact version. In its package directory, read AGENTS.md, docs/cli/automation.md and docs/c15t-setup.md. Use them for account, organization and project commands and for JSON response handling. Use the c15t docs in step 5 for packages, versions and APIs; the CLI's setup guide can lag behind c15t releases.

## 2. Inventory the application

Do this before installing anything; the result decides which path step 5 takes.

1. Find the target app, framework, router, rendering mode (server, static, cached or single-page), package manager, styling and languages. In a monorepo with more than one candidate app, ask which one.
2. Classify the existing consent setup. Walk this tree:

   ```
   Does the target app's package.json, or what it resolves to in the lockfile, list
   c15t, @c15t/nextjs, @c15t/react or @c15t/scripts? Other workspaces don't count.
   ├── Yes, every c15t package is 3.x → keep it; configure hosted mode in step 5.
   ├── Yes, any c15t package is below 3.0 → UPGRADE path (step 5a).
   └── No
       ├── Another consent manager or a homegrown banner exists → REPLACE path (step 5b).
       └── Nothing → INSTALL path (step 5c).
   ```

   A homegrown banner counts as a consent manager: a component or script that shows a choice, stores it (cookie or localStorage) and checks it before tracking.
3. Inventory every optional tool. Search dependencies, imports, root layouts and document heads, inline scripts, framework plugins, environment variable names, vendor domains and IDs, iframes and embeds, and tag-manager containers. Include first-party tracking such as a fetch to an app-owned metrics endpoint. For each tool, record:
   - every file that loads or initializes it
   - every file that sends events through it
   - its IDs and its purpose (measurement, marketing, functionality, experience)
   - whether the current code gates it on consent, and how
4. Read the site's own requirements (PROJECT.md, README, AGENTS.md, legal notes). Write down whether vendors may load with denied defaults before consent or must send no requests at all before opt-in. If nothing says, ask. Step 6 picks integrations by this answer.

Show the inventory as a short checklist and continue.

## 3. Connect the Inth account

Run `inth auth status --json` and `inth whoami --json`. If a new connection is needed, ask for the email to use and for permission to request organization and project read/write access. Follow the bundled agent authentication guide to run `inth signup` or `inth login` with those scopes. Show the approval link and code, then run `inth login --complete --wait --json` in a background terminal; the user approves in the browser. Continue steps 2 and 5 while waiting.

## 4. Set up the hosted project

Find and reuse the intended organization and project with the CLI; create them only when none fits. Ask if ownership is unclear or a new project's hosting region is undecided. Use a region returned by `inth region list`.

Read the project with `inth project get <id> --json`. Use `data.data.consent.backendUrl` verbatim as the runtime URL. If it is null, stop configuring the app and inspect provisioning. Never construct a backend URL yourself.

Trusted origins must include every host the app runs on: production, previews and the local development host and port. Update them with the full list, keeping every existing entry, because `project update` replaces the list. Keep CLI credentials and management API keys out of application code, public environment variables and source control. Ask before changing billing.

## 5. Install or upgrade c15t

Resolve exact versions first. Look up `c15t@latest`, and `@c15t/integrations@latest` when any vendor helper is used, with the project's package manager:

| Package manager | Version lookup |
| --- | --- |
| npm | `npm view <package>@latest version` |
| pnpm | `pnpm view <package>@latest version` |
| Yarn 1 | `yarn info <package> dist-tags.latest` |
| Yarn 2+ | `yarn npm info <package>@latest --fields version` |
| Bun | `bun info <package>@latest version` |

Install other packages the quickstart lists, such as `svelte` or `@astrojs/svelte`, with the quickstart's own specifiers; the c15t dist-tag does not apply to them. The two c15t packages are numbered separately, so their versions can differ. Install each at its exact resolved version with the project's package manager. Never install by tag or without a version. An untagged install resolves npm's default tag, which can be a different major version. A tagged install can resolve to an older release per package when the package manager delays new releases (pnpm's `minimumReleaseAge`), and mixing releases installs two copies of the consent engine. After installing, check that exactly one version of `@c15t/core` is installed (`npm ls @c15t/core`, `pnpm why @c15t/core`, `yarn why @c15t/core`, `bun why @c15t/core`, or the lockfile). Two copies keep two separate consent states. Do not add overrides or resolutions to force it; install the versions the dist-tag resolves instead.

After installing, read `node_modules/c15t/SKILL.md` and `node_modules/c15t/AGENTS.md`. They index the bundled docs for the installed version. Read the bundled choose-your-setup page and the full quickstart for this framework before writing code. If the bundled docs are missing, use `https://c15t.com/docs/concepts/choose-your-setup.md` and `https://c15t.com/docs/frameworks/<framework>/quickstart.md`. Check each API you use against the installed package's exports and types.

### 5a. Upgrade path

Upgrade before any other change. v3 renamed most v2 APIs, and editing v2 code by hand produces a mix of both. Read `https://c15t.com/docs/frameworks/<next|react|javascript>/upgrade-v3.md`, or `https://c15t.com/docs/upgrade-v3.md` for other frameworks, and follow it in order. Always run its codemod command exactly as the guide writes it, with every listed transform, first with `--dry-run`, then for real, before editing c15t code by hand. The transforms depend on each other (one renames components, another rewrites import paths), so a subset leaves broken imports. They also cover every file, including ones you have not read; hand edits miss them. Then resolve every `TODO(c15t v3)` the codemods leave. An Inth-hosted backend needs no change. If the app runs its own `@c15t/backend`, stop and tell the user: a v3 client cannot read a v2 backend, so both must ship together.

After the upgrade, point the app at the Inth project's backend URL if it used another one.

### 5b. Replace path

Record the old consent manager's categories, storage key, callbacks and every caller that reads its state. Map each old category to a c15t category by purpose, not by name. Then install c15t (5c) and, in step 6, move every caller to c15t and delete the old banner, its storage reads and its callbacks. Old stored choices do not carry over unless the c15t docs describe an import; visitors choose again.

### 5c. Install path

Configure hosted mode with the backend URL through the app's existing environment conventions as a public variable. Mount one consent provider at the app root, outside route components; two providers keep two separate choices. Render the banner and the preferences dialog. Add a persistent control that reopens preferences, such as a footer link. If the app already has one (from the old banner), rewire it instead of adding a second. Match the app's design, languages, keyboard access, narrow screens, color schemes and reduced motion.

## 6. Move every tool behind consent

Work one tool at a time from the inventory of optional tools.

1. **Read before editing.** Read the bundled integrations overview (`node_modules/c15t/docs/integrations/overview.md`, or `https://c15t.com/docs/integrations/overview.md`), then the tool's full guide and the framework's scripts guide. The overview lists each helper's loading behavior; a search excerpt does not. Note the package version, the guide you read, the helper's import, its required options, and what it does before consent and on withdrawal. Never infer that no helper exists because the app does not use one.
2. **Pick the mechanism against the site's requirement.**
   - A `@c15t/integrations` helper exists and its loading behavior meets the site's requirement for requests before opt-in → use it through the provider's documented scripts option.
   - The helper loads before a choice (for example "Always loads; signals Google consent") and the site forbids requests before opt-in → do not use it silently. Use the documented gated alternative if the guide gives one; otherwise register a custom script with a c15t category that waits for consent, and tell the user which vendor features that loses (such as consent-mode modeling).
   - An embed or iframe → use the documented consent-gated component.
   - No helper → register a custom script or gate on the documented consent hook, with a category chosen by purpose.
3. **Make c15t own the loader.** Remove the old script tag, SDK bootstrap, framework component (such as `@next/third-parties`), noscript pixel, plugin and old consent callback for that tool. Wrapping an old loader in a consent check is not a migration: it misses withdrawal and leaves two loading paths. Consent checks can still guard event calls.
   **Framework vendor packages** (`@next/third-parties`, `@nuxt/scripts`, `nuxt-gtag`, `vue-gtag`, `react-ga4`, Gatsby and Astro analytics plugins) load the vendor outside c15t. Replace every vendor export the app uses, then remove the package or module once nothing else uses it:

   | Export | Replace with |
   | --- | --- |
   | `GoogleAnalytics` (`@next/third-parties`), `useScriptGoogleAnalytics` (`@nuxt/scripts`), the `nuxt-gtag` module | The `gtag` helper, or a custom c15t script when the site forbids requests before opt-in |
   | `GoogleTagManager`, `useScriptGoogleTagManager` | The `googleTagManager` helper, or a custom c15t script under the same rule |
   | Other `@nuxt/scripts` registry scripts (`useScriptMetaPixel`, `useScriptPlausibleAnalytics`, …) | That vendor's `@c15t/integrations` helper, or a custom c15t script |
   | `useScriptTriggerConsent` and other consent triggers | Nothing: c15t decides when the script loads. A second consent trigger keeps a second source of truth. |
   | `sendGAEvent`, `sendGTMEvent`, `useGtag().gtag`, a registry script's `proxy` | Calls to the `gtag`, `dataLayer` or vendor global that c15t loads. `sendGAEvent` drops events with a console warning once `GoogleAnalytics` is gone. |
   | `YouTubeEmbed`, `GoogleMapsEmbed`, `ScriptYouTubePlayer`, `ScriptGoogleMaps` | The YouTube or Google Maps guide: your own iframe inside the framework's consent gate |

   Keep the measurement ID, container ID and `dataLayer` name the old code used. `@nuxt/scripts` can also load necessary first-party scripts; keep those registrations.
4. **Keep the product's events.** Keep vendor IDs, settings, event names and payloads. Point each event caller at the loader c15t owns, and make sure the call is a no-op or queued by the vendor's own documented queue while consent is missing. Never add a queue that replays events collected without consent, replace tracking with empty functions, or keep a second SDK bootstrap to preserve an event API. Check for module-level SDK calls that run before consent is known.
5. **Withdrawal must stop the tool.** Follow the guide's revocation behavior (opt-out API, reload, or unload). A one-time gate at startup is not enough.
6. **Tag managers.** Inspect the container's tags too. Non-Google tags ignore Consent Mode; give each a consent requirement, or move it out of the container to its own helper. Expose the categories the downstream tags need in the c15t UI. Report container changes you cannot make yourself with an owner and the exact next step.
7. **Search again.** For each tool, search the app and shared packages for its ID, domain, global (`gtag`, `fbq`, `posthog`) and old loader. Every remaining match must have a stated role. Keep one loading path per tool. Remove vendor packages from package.json once nothing imports them (for example `@next/third-parties`, `posthog-js`, `@c15t/scripts`); a leftover dependency invites someone to load the vendor again.

Naming: follow the app's conventions. Name consent code for consent (`ConsentProvider`), vendor configuration for the vendor, and event functions for the product action (`trackSignup`). Do not prefix app code with `C15T`, and avoid names like `ConsentAwareAnalytics` or `AnalyticsV2`. Keep exported names other code depends on. Use c15t's documented category values. Remove old consent state, files and dependencies only after checking their remaining consumers. Server-side tracking and forwarding are outside the browser consent boundary; list them separately.

Denied consent does not always mean zero network traffic, and choosing c15t does not make the site legally compliant. State the behavior you configured; do not claim compliance.

## 7. Verify in a browser

Run the project's typecheck, tests and production build. Serve the production build from a trusted origin and use a fresh browser profile for each journey. Follow the bundled guides/verify-consent page (`https://c15t.com/docs/guides/verify-consent.md`). Check, by network requests and storage rather than by what the page shows:

1. First visit: under a policy that asks for a choice, the banner shows; under any policy, no optional tool sends a request that the site's requirement forbids. Also check a location without a prompt as the guide describes: no banner is correct there. Then block the backend URL: no banner shows and tools that wait for consent send no requests. An always-loading helper still loads in these cases; check that it signals denied consent.
2. Reject all: tools that wait for consent send no requests, and always-loading helpers signal denied consent; the choice is written to the backend (a successful POST); it survives a reload and a client-side navigation.
3. Accept all: each tool loads once, its app events fire once per action, and the choice survives a reload.
4. Withdraw: reopen preferences, reject, reload; the tools stop.
5. Granular choices, keyboard use and a narrow viewport.

A closing banner does not prove a saved choice; wait for the backend response. Reconcile what you observed with the step 2 inventory and investigate any request you did not expect. Fix failures within this task and mark anything you could not access as unverified.

## 8. Hand back

Report:
- changed files, organization and project IDs, dashboard link, backend URL and trusted origins
- the c15t version installed and which path you took (install, upgrade or replace)
- the inventory, with each tool's helper or documented exception, removed loading paths, kept event callers and the checks you ran
- what is provisioned, integrated, browser-verified, unverified or blocked, and who owns each remaining external change

Keep the change focused. Deploy only through the project's authorized workflow.
