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
specs/spec.live/README.md
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

## Spec-builder healthz diagnosis

The first `spec.healthz` builds failed before writing files. The causes and
fixes were:

| Attempt | Result | Cause | Fix |
| --- | --- | --- | --- |
| `spec.health@1` | failed | Workbench Model Gateway not configured. | Restored the controller to the local gateway. |
| `spec.healthz@1`, `@2` | failed at 24 turns | The empty spec package did not exist, so `list_files .` failed and the model kept retrying. | Create the package directory before the model starts. |
| `spec.healthz@3` | failed after 120 s | The harness broker request timeout was 120 s; Beam Free took longer. | Set the harness timeout to 600 s and the controller timeout to 900 s in the live run; Compose now defaults both to 600 s. |
| `spec.healthz@4` | failed at 64 turns | The package still did not exist; the prompt had no version or concrete YAML shape. | Create the package, pass `spec id@version`, and add a valid prompt example. |
| `spec.healthz@5` | failed at 64 turns | Package still missing before the directory fix. | Create the package before the model call. |
| `spec.healthz@6` | failed at 64 turns | Package existed but the prompt still lacked the target version and a concrete YAML shape, so the model kept listing. | Pass the version and add the valid YAML example. |
| `spec.healthz@7` | draft | Strict validation passed with no errors or warnings. | Recorded as `spec_068a38bf-1a4c-4bc7-80f6-6baea46aafe9`. |

The resulting package is [specs/spec.healthz](specs/spec.healthz). It contains
`spec.yaml`, a human README with three Mermaid views and source links, and the
context, containers and components diagrams. It is a draft pending human
approval.

The product changes are PRs #61 (request timeout) and #62 (package creation,
controller version, prompt) in `local-ai-tools`. PR #63 tightens the version
parser, uses workspace-relative `specs/<id>/` paths in the prompt and
coordinates the controller timeout with the harness timeout.

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
