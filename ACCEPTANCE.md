# Live acceptance result

This branch is the result of a live Agent Swarm spec-to-merge run on
2026-11-22. The controller, worker, harness, gates and Git operations were real
Swarm components. The model was the free **Beam Free** model from
opencode-go, reached through the Workbench Model Gateway.

## Result

| Item | Value |
| --- | --- |
| Branch | `e2e/live-20261122-01` |
| Spec | `spec.live@5` |
| Spec record | `spec_32007222-a3db-4252-b578-868b2d13c907` |
| Final plan | `plan_23e944bf-2660-49ba-8410-2979d64b9cae` |
| Coder task | `task_42942f5d-748e-4ef4-92d1-872e5d1a9209` |
| Coder attempt | `att_6ffe863a-452b-490f-a253-b4a7b7203009` |
| Tester task | `task_ead479d6-2f6b-44ec-baa7-6d606216ef9a` |
| Security task | `task_6b9d34d1-602e-46bb-bf1b-90618db6ac4e` |
| Model | `remote/beam-free` (`opencode-go::beam-free`) |
| Coder usage | 7,006 input / 908 output / 0 cache tokens |
| Coder cost | 0.0000 USD |
| Fixture test | `npm test` exit 0, 1 test passed |

## Files

```text
specs/spec.live/spec.yaml
specs/spec.live/diagrams/context.mmd
specs/spec.live/diagrams/containers.dsl
specs/spec.live/diagrams/components.dsl
src/greet.js
test/greet.test.js
```

```js
// src/greet.js
export default function greet(name) {
  return `Hello, ${name}!`;
}
```

```js
// test/greet.test.js
import { test } from 'node:test';
import assert from 'node:assert/strict';
import greet from '../src/greet.js';

test('greet returns greeting', () => {
  assert.equal(greet('World'), 'Hello, World!');
});
```

## Checks

- Coder check `check_coder_test`: `test -f src/greet.js && test -f test/greet.test.js && npm test` — exit 0.
- Tester gate `check_tester_test`: `npm test` — exit 0.
- Security gate `check_security_scan`: `test -f src/greet.js && ! grep -R -n "eval(" src test` — exit 0.
- Merge approval and merge were blocked until the tester and security gates completed.
- The coder commit carries the required `Agent-*` provenance trailers.

## Commits

```text
dbc6e1b Merge agent/integration into e2e/live-20261122-01
b22dd8b Merge task_42942f5d-748e-4ef4-92d1-872e5d1a9209: Implement greet and its unit test
c2d7cc3 feat: Implement greet and its unit test
```

## Notes

- The spec package was authored and strictly validated before the run; the
  local 4B model could not produce a schema-valid package. The remote Beam Free
  model authored the plan draft, and the final plan was corrected to make the
  gate tasks use `gateFor` without a deadlocking `dependsOn` on the coder.
- The coder and final code were produced by Beam Free through the live
  controller/worker path.
- opencode-go requires an `x-opencode-session` header. This run used a loopback
  compatibility proxy in front of the Workbench gateway; native provider-header
  support is tracked separately.
- The remote model cost is zero because Beam Free is a free opencode-go model.
