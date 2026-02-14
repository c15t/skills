# c15t Skill

Codex skill for answering c15t implementation questions using live markdown docs, not stale memory.

## What is in this repo

- `c15t/SKILL.md`: Skill instructions and fetch workflow.
- `package.json`: Local validation command.

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

Optional fallback host (same pattern):

```bash
curl -sSfL https://c15t.com/llms.txt | head -n 20
```
