# The Box Communication Bridge

## Purpose and scope

This repository is a lightweight, asynchronous communication channel between ChatGPT and Codex. It is **not** The Box project repository.

- The Box source, experiment data, raw evaluation artifacts, credentials, and private configuration stay in the local The Box repository.
- This repository contains coordination instructions, task documents, concise status, and handoff messages only.
- Do not assume access to this bridge grants access to The Box source.

## Working protocol

1. Pull the latest bridge repository and read this file before acting.
2. Read the relevant task document in `tasks/` in full.
3. Task-specific instructions, findings, and completion reports belong in their own `tasks/task-NN.md` file. Do not append lengthy task material to this file.
4. Keep this file as a small index and current coordination record.
5. Append new information to the relevant task file; preserve prior decisions and reports. Only one participant edits a given file at a time.
6. Commit and push changes. The other participant retrieves the latest version before responding.
7. If a push conflicts, pull and reconcile. Never force-push.
8. Codex must not begin work on The Box unless an explicit task authorizes it. The user approves changes to project scope, architecture, experiment design, and implementation.

## Roles

- **ChatGPT:** research/design reasoning, task drafting, and review of Codex findings.
- **Codex:** local inspection, implementation, experiments, tests, and reporting when explicitly tasked.
- **User:** directs work and approves consequential changes.

## Task index

| Task | Document | Status | Summary |
|---|---|---|---|
| C2 | [Bridge verification commit](https://github.com/armanakh/git_practice/commit/02a356b6d4d5f351993dd53957bf77537dd6b5f8) | PASS | Verified ChatGPT-to-Codex-to-ChatGPT round trip. |
| 21 | [tasks/task-21.md](tasks/task-21.md) | COMPLETE | Fresh 900-episode FiT diagnostic evaluation; detailed findings archived. |
| 22 | [tasks/task-22.md](tasks/task-22.md) | DESIGN RESOLVED; IMPLEMENTATION NOT AUTHORIZED | Read-only inspection complete. Training design decisions resolved in Section 9; separate implementation task required. |
| 23 | [tasks/task-23.md](tasks/task-23.md) | COMPLETE; COVERAGE LIMITATION IDENTIFIED | Implementation and bounded validation complete: 247 tests passed. Model-based selection occurred in only 3/96 decisions; full three-action coverage in 4/75 states. Reviewed; no held-out evaluation yet. |
| 24 | [tasks/task-24.md](tasks/task-24.md) | READ-ONLY DIAGNOSTIC AUTHORIZED | Diagnose sparse exact-state/action coverage and determine whether more training or a representation change is needed. No source changes or experiments. |

## Current coordination status

- Bridge round trip is verified.
- The Box source repository remains local and separate.
- Communication is asynchronous and user-mediated: the user initiates Codex work and signals when a response has been pushed.
- **Current research state:** Task 21 is complete. Task 22 design is resolved. Task 23 implementation and bounded training validation are complete (247 tests passed). Review found only 3 model-based decisions in 96 training decisions and complete three-action estimates in 4 of 75 observable states. Task 24 read-only diagnostic is now authorized. No held-out policy comparison is authorized.
- **Task 22 resolved design / Task 23 implementation:** seeded agent-owned selection among unestimated actions (no quotas/rotation); pre-action predictions; post-transition structured evaluator feedback; learned outcome vector ordered lexicographically by health, then quality, then oxygen, without scalar reward; INSPECT treated as information whose value is assessed downstream; preserve FiT mechanics and Box core; freeze memory and disable feedback, learning, and exploration for held-out evaluation. Task 23 authorizes implementation and bounded training validation only. See Section 9 of Task 22.

## Message log

### Initial setup and bridge verification

The bridge was established without uploading The Box source. Codex independently cloned/read this repository, acknowledged the scope, and pushed a response. ChatGPT verified Codex's commit. See the [verified acknowledgement](https://github.com/armanakh/git_practice/commit/02a356b6d4d5f351993dd53957bf77537dd6b5f8).

### Task 21

Task specification, Codex completion report, detailed diagnostic extract, evidence identities, and review conclusion are archived in [tasks/task-21.md](tasks/task-21.md). The full raw Task 21 JSON artifacts remain local in The Box repository and were not uploaded to this bridge.

### Task 22

The agreed structured-training direction and read-only repository inspection request are in [tasks/task-22.md](tasks/task-22.md). Codex is asked to inspect feasibility and report a concrete implementation proposal. Source changes and experiments are explicitly prohibited until the user reviews the report and authorizes a separate implementation task.


### Task 22 design resolution

The read-only inspection report was reviewed and the remaining design decisions were resolved in Section 9 of [tasks/task-22.md](tasks/task-22.md). A separate implementation task is the next step; implementation is not yet authorized.

### Task 23

[Task 23](tasks/task-23.md) authorizes implementation in the local The Box experiment/agent layer, focused tests, full test suite, and a bounded training-mechanics validation in a new output location. It explicitly prohibits held-out policy comparison, baseline changes, Box core/FiT dynamics changes, and uploading source or raw artifacts to this bridge.

### Task 23 review and Task 24

Task 23 implementation is accepted as complete for mechanics, but its small validation did not establish useful model-based coverage. See the review note appended to [tasks/task-23.md](tasks/task-23.md). [Task 24](tasks/task-24.md) asks Codex to diagnose exact-key sparsity and training/generalization options using existing code and artifacts only. No changes or new experiments are authorized.
