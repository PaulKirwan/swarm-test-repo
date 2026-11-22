# Swarm E2E Test Repo

Minimal fixture for the Agent Swarm spec-to-merge acceptance run.

This branch includes the live Beam Free acceptance result. See
[`ACCEPTANCE.md`](ACCEPTANCE.md) for the spec, model, task IDs, checks and
commits.

The fixture intentionally started with no feature code. The acceptance run gave
the Swarm a brief and produced the implementation and its test.

## Test command

```bash
npm test
```

`npm test` runs `node --test`.
