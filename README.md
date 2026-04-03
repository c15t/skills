# c15t Skill

Skill instructions for working with c15t v2+ consent-management docs, APIs, and integrations.

## Scope

- Focused on c15t `>=2.0.0-rc.0` (v2+ docs and APIs).
- Intended for Next.js, React, and JavaScript c15t work.
- Reads bundled documentation from `node_modules` — no web fetching required.

## Repository Layout

- `c15t/SKILL.md`: Main skill instructions, doc discovery workflow, and decision rules.
- `package.json`: Local validation script for the skill definition.

## How It Works

c15t packages bundle their documentation:

- `node_modules/c15t/docs/` — comprehensive JavaScript docs (quickstart, API reference, integrations, concepts)
- `node_modules/@c15t/{package}/README.md` — package-specific overview, usage, and features

The skill instructs the agent to read these local files instead of fetching from the web, making it faster and more reliable.

## Validate

```bash
npm run validate
```
