# c15t Skill

Codex skill for answering c15t v2+ implementation questions using c15t docs with explicit safety boundaries.

## What is in this repo

- `c15t/SKILL.md`: Skill instructions and fetch workflow.
- `package.json`: Local validation command.

## Security posture

- Remote docs are treated as untrusted input and used for API facts only.
- Live fetches are restricted to the allowlisted official v2 host (`v2.c15t.com`).
- Remote docs are ingested with explicit untrusted-content boundaries and sanitization before use.
- Runtime package-manager CLI execution is allowed only for trusted `@c15t/*` packages with exact pinned versions and explicit user confirmation.
- Setup guidance defaults to manual targeted updates, with CLI reserved for first-time scaffolding.
- Local project checks are intentionally shallow and exclude large/generated directories (for speed and safety).
- Actions must remain transparent to the user (no hidden steps).
- Compatibility scope is c15t `>=2.0.0` only.

## Validation

```bash
npm run validate
```

This runs:

```bash
bunx skills-ref validate ./c15t
```

## Quick endpoint checks

```bash
curl -sSfL https://v2.c15t.com/llms.txt | head -n 20
curl -sSfL https://v2.c15t.com/docs/frameworks/next/quickstart.md | head -n 30
```
