# c15t Skill

Skill instructions for working with c15t consent-management docs, APIs, and integrations, across both major versions.

## Scope

- Detects the installed c15t major version first, then branches: v3, v2, or nothing installed yet.
- v3 projects: defers to the version-matched `SKILL.md`/`AGENTS.md` bundled inside the installed `c15t` package, rather than duplicating v3 guidance here.
- v2 projects (`>=2.0.0-rc.0`): offers the v3 upgrade guide, then falls back to v2-specific doc discovery and decision rules if the user stays on v2.
- Nothing installed: resolves and pins an exact v3 version from the `alpha` dist-tag before installing.
- Intended for Next.js, React, and JavaScript c15t work.
- Reads bundled documentation from `node_modules` for an installed project. Adding c15t to a project with nothing installed needs one registry lookup to resolve the current alpha version.

## Repository Layout

- `c15t/SKILL.md`: Main skill instructions, version detection, and per-version decision rules.
- `package.json`: Local validation script for the skill definition.

## How It Works

c15t packages bundle their documentation, matched to the installed version:

- v3: `node_modules/c15t/SKILL.md` and `node_modules/c15t/AGENTS.md` index the full docs set.
- v2: `node_modules/c15t/docs/` has comprehensive JavaScript docs (quickstart, API reference, integrations, concepts); `node_modules/@c15t/{package}/README.md` has package-specific overview, usage, and features.

The skill instructs the agent to read these local files instead of fetching from the web, making it faster and more reliable.

## Validate

```bash
npm run validate
```
