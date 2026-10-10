# c15t Skill

Skill instructions for working with c15t consent-management docs, APIs, and integrations, across both major versions.

## Scope

- Detects the installed version of every c15t runtime package the app resolves, then branches: v3, v2, pre-v2, a half-finished upgrade, or nothing installed yet.
- v3 projects: defers to the version-matched `SKILL.md`/`AGENTS.md` bundled inside the installed v3 package (`c15t`, or a framework package such as `@c15t/nextjs`), rather than duplicating v3 guidance here.
- v2 projects (`>=2.0.0-rc.5`): offers the v3 upgrade guide, then falls back to v2-specific doc discovery and decision rules if the user stays on v2.
- Pre-v2 projects (below `2.0.0-rc.5`): not covered; offers to replace c15t with a fresh v3 install.
- Nothing installed: resolves and pins an exact v3 version from the `alpha` dist-tag before installing.
- Intended for Next.js, React, and JavaScript c15t work.
- Reads bundled documentation from the installed packages: `node_modules`, or Yarn's Plug'n'Play archives read through `yarn node`. Adding c15t to a project with nothing installed needs registry lookups to resolve the current alpha versions.

## Repository Layout

- `c15t/SKILL.md`: Main skill instructions, version detection, and per-version decision rules.
- `package.json`: Local validation script for the skill definition.

## How It Works

c15t packages bundle their documentation, matched to the installed version:

- v3: each package's `SKILL.md` and `AGENTS.md`, such as `node_modules/c15t/AGENTS.md`, index the full docs set.
- v2: `node_modules/c15t/docs/` has comprehensive JavaScript docs (quickstart, API reference, integrations, concepts); `node_modules/@c15t/{package}/README.md` has package-specific overview, usage, and features.

The skill instructs the agent to read these local files instead of fetching from the web, making it faster and more reliable.

## Validate

```bash
npm run validate
```
