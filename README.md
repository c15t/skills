# c15t Skill

Agent skill for setting up hosted c15t consent management with the Inth CLI.

## Scope

- Targets the c15t release on the `latest` npm dist-tag (v3).
- Covers the Inth account and project, installing c15t, upgrading from v2, replacing another consent banner, moving analytics, pixels and embeds behind consent, and browser verification.
- Mirrors the hosted skill at [c15t.com/SKILL.md](https://c15t.com/SKILL.md). The only difference is the skill name, which stays `c15t` so existing installs keep working.

## Repository Layout

- `c15t/SKILL.md`: the setup steps.
- `package.json`: local validation script for the skill definition.

## How It Works

The skill walks the agent through eight steps: read the Inth CLI docs, inventory the app, connect the Inth account, set up the hosted project, install or upgrade c15t, move every optional tool behind consent, verify in a browser, and hand back.

For c15t APIs it points the agent at the docs bundled with the installed package (`node_modules/c15t/SKILL.md`, `node_modules/c15t/AGENTS.md` and `node_modules/c15t/docs/`), with `https://c15t.com/docs/` as the fallback.

## Validate

```bash
npm run validate
```
