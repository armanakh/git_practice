# Task 23: Implement Structured Agent Training

**Status:** IMPLEMENTATION AUTHORIZED  
**Depends on:** Task 22 read-only inspection and Section 9 design resolution  
**Repository:** Local The Box repository only. The GitHub bridge remains a coordination channel and must not receive Box source, private configuration, or raw experiment artifacts.

## 1. Objective

Implement the Task 22 structured training design in the FiT experiment/agent layer.

The immediate objective is to give the existing FiT agent a genuine opportunity to learn from its own action choices, pre-action predictions, actual transitions, and transparent evaluator feedback. The implementation must support a fair test of whether this training improves independent performance against the unchanged fixed-threshold baseline.

This task authorizes source changes, tests, and a bounded training-mechanics validation run as described below. It does **not** authorize a held-out policy-comparison evaluation or any change to Box core, FiT world dynamics, agent observation permissions, or baselines.

## 2. Authoritative design

Read Task 22 in full, especially Sections 2 and 9. Section 9 resolves the design decisions and is authoritative where it elaborates the original inspection request.

### 2.1 Preserve boundaries

Implement in the FiT experiment/agent layer only.

Do not change:
- Box core, authoritative state ownership, capability/proposal contracts, or relationship model;
- FiT state-transition mechanics, health rules, quality/oxygen dynamics, delayed penalties, history-dependent effects, or action semantics;
- agent observation permissions or reveal timing;
- existing fixed-threshold and experience-disabled baselines;
- historical experiment artifacts or their meaning.

Do not introduce counterfactual outcomes, hidden-state access for the agent, or a scalar reward.

### 2.2 Agent-owned uncertainty-directed training policy

The current missing-estimate fallback to WAIT is not a genuine learned preference. During this new training phase:

- When one or more actions available to the agent lack a usable estimate for the current observable state, select among the unestimated actions using a seeded pseudorandom choice.
- Do not impose action quotas, equal frequencies, rotations, target distributions, or experimenter-selected actions.
- When all available actions have estimates, select according to the learned outcome vector in Section 2.3.
- When estimated vectors tie, use a seeded reproducible random tie-break.
- Record the selection mode for every decision: uncertainty-directed, model-based, or tie-break.
- Record the random seed and enough decision metadata to reproduce the policy choices.

This is an agent training policy, not an experimenter override. It may produce unequal action counts. Do not describe uncertainty-directed selections as learned preferences.

Keep this policy isolated from evaluation. Do not change the existing baselines.

### 2.3 Structured outcome model and action comparison

For each existing observable-state/action experience key, estimate the three observed transition components:
1. fish-health change;
2. tank-quality change;
3. oxygen change.

Reuse the existing running-estimate mechanism where feasible. Predictions must represent expected component changes, not an invented combined score.

Compare candidate actions lexicographically:
1. maximize predicted fish-health change;
2. only if health predictions tie, maximize predicted quality change;
3. only if both tie, maximize predicted oxygen change.

Keep component predictions separately visible in records. Do not use weighted sums or scalarization.

Important implementation check: inspect how current transition estimates handle bounded/clipped state changes, hidden condition/history effects, and inspection-result keys. Ensure the comparison uses the actual semantics of the existing estimates. If a sound lexicographic comparison cannot be implemented without changing the agreed objective or making an unsupported assumption, stop and report the exact issue rather than silently inventing a solution.

### 2.4 Pre-action prediction and post-transition evaluator

Enforce this order for each training decision:

1. Capture the agent's permitted observation.
2. Select the action.
3. Capture the prediction before execution.
4. Execute the action through the existing FiT/Box path.
5. Read the committed transition through the experiment-side record/observer.
6. Generate structured evaluator feedback.
7. Deliver permitted feedback to the agent and update its experience from the actual executed transition.

The prediction record must not be able to access the transition or evaluator feedback from the same decision.

Evaluator output must be based on the actual committed transition. It is read-only and must not mutate authoritative state.

### 2.5 Evaluator assessment

Provide structured feedback for:
- fish health: gain, no change, or loss;
- tank quality: improved, unchanged, or degraded;
- oxygen: improved, unchanged, or degraded;
- an overall transparent description: health-positive, health-negative, or mixed, with the component facts that justify it.

The agreed objective hierarchy is health primary, quality supporting, oxygen supporting. Do not turn this into a scalar reward.

Feedback must distinguish direct action effects, ordinary environmental changes, and delayed/history-dependent effects where the existing transition records support that distinction. Describe delayed consequences when they actually materialize. Do not attribute effects to an action beyond what the recorded mechanics establish.

The categorical assessments are explanatory labels derived from observed transition data. They are not extra reward values. Store the assessment alongside the transition/learning record so that the learning process is auditable. Update numerical experience from the actual pre/post observation pair and executed action, not from invented evaluator scores.

Keep agent-facing feedback separate from experimenter-only diagnostics. Never expose unrevealed hidden condition, hidden history, pending penalties, or other information outside the existing observation/reveal contract.

### 2.6 INSPECT

Treat INSPECT as an information action.

Feedback must separately report:
- immediate physical transition, if any;
- information revealed by the action;
- when that information becomes available to subsequent agent decisions.

Do not award an arbitrary immediate information bonus or label INSPECT a loss solely because it did not improve physical state. Do not reveal hidden information before the existing INSPECT mechanism legitimately reveals it.

Assess its value at episode level through subsequent decisions and outcomes. Do not claim it was beneficial merely because information was revealed.

### 2.7 Training/evaluation separation

This task implements the training mechanism and its boundary. Before held-out evaluation:
- snapshot/freeze learned estimates;
- disable evaluator feedback;
- prohibit memory/parameter updates;
- disable uncertainty-directed exploration;
- use the frozen model-based selector;
- explicitly record missing action/state coverage and identify any deterministic fallback decisions as fallback, not model-based choices.

The existing experience-disabled and fixed-threshold baselines remain unchanged.

Do not run the held-out comparison evaluation under this task. That requires a separate task after review of the implementation and training validation.

## 3. Training scenario design and bounded validation

Use existing FiT dynamics and valid scenario configuration mechanisms. Do not modify world mechanics to manufacture action outcomes.

Inspect the existing Task9 training probes and scenario-generation utilities. Replace experimenter-prescribed first-action probes for the new structured-training path with agent-directed decisions. Design a training manifest with repeated/relevant observable starting contexts where existing configuration support permits, so seeded uncertainty-directed choices can accumulate action experience across episodes.

The manifest should include supported variation across:
- NORMAL and STRESSED conditions;
- tank states below, around, and above the health-relevant threshold;
- varied fish health;
- valid histories and inspection-relevant contexts supported by existing scenario constructors.

Do not force action selection within a scenario. Do not guarantee or target action counts. Record scenario opportunities/configuration coverage separately from actual action counts. If the current scenario constructors cannot provide a claimed opportunity, report the limitation rather than modifying FiT dynamics.

After implementation and tests, run only a bounded **training-mechanics validation** using a new, clearly named output location. It may verify that training completes, feedback/records are produced, memory updates occur, and action selection is reproducible. It must not be presented as a performance evaluation or baseline comparison. Preserve all outputs; do not overwrite historical artifacts.

If the validation requires a potentially destructive command or would overwrite any existing artifact, stop and report before running it.

## 4. Required records and reporting

Persist sufficient structured records to audit:
- training episodes and decisions;
- scenario/configuration identity and opportunity coverage;
- actual action counts and proportions;
- action-selection mode per decision;
- pre-action prediction vector and whether each component was available;
- actual observed component changes;
- evaluator categories and explanation;
- information revealed by INSPECT and its availability timing;
- experience/memory updates;
- learned memory coverage by observable state and action;
- random seed and reproducibility metadata.

Keep separate:
- prediction accuracy;
- evaluator assessment distribution;
- action distribution and selection modes;
- scenario opportunity coverage;
- memory coverage;
- training task outcomes.

Do not collapse these into one score.

## 5. Required tests

Add focused tests proving:

1. Pre-action predictions are captured before execution and cannot see same-decision evaluator output.
2. Evaluator reads committed transitions and cannot mutate authoritative state.
3. Agent-facing feedback does not leak unrevealed condition, history, pending penalties, or hidden state.
4. Only actually executed transitions update experience.
5. Health, quality, and oxygen estimates are separately represented.
6. Lexicographic comparison obeys health, then quality, then oxygen, with no scalarization.
7. Uncertainty-directed selection chooses only among currently unestimated available actions, uses a reproducible seed, and imposes no quotas or action-frequency targets.
8. Model-based selection occurs only when all required action estimates are available.
9. Ties are resolved reproducibly and recorded as tie-breaks.
10. INSPECT feedback separates physical effects from revealed information and respects reveal timing.
11. Delayed consequences are described when they materialize, not prematurely.
12. Evaluation snapshot is frozen; feedback, learning, and exploration are disabled.
13. Missing evaluation coverage and fallback choices are explicitly recorded.
14. Existing baselines and FiT mechanics remain unchanged.

Add tests at the narrowest relevant layers. Keep existing tests passing.

## 6. Implementation discipline

- Inspect the local repository before editing and follow its established conventions.
- Keep changes minimal and localized.
- Do not refactor unrelated code.
- Do not weaken, delete, or rewrite existing tests merely to make the new implementation pass.
- Do not alter historical experiment results or silently change existing Task9/Task10/Task18/Task21 interpretation. If a new runner is cleaner than modifying an old one, add the new runner and preserve legacy behavior.
- Run the focused new tests first, then the full existing test suite.
- Report exact commands, test counts, and results.
- If an implementation detail conflicts with this task or Task22 Section 9, stop and report before making a design change.

## 7. Completion report

Append a completion report to this task document and push it to the bridge. Include:

1. Files changed and purpose of each change.
2. Architecture and policy implemented, with concrete symbols/paths.
3. Confirmation of preserved Box/FiT/baseline boundaries.
4. Tests added, exact commands, counts, and results.
5. Training manifest design and supported opportunity coverage.
6. Bounded validation command, seed, episode/decision counts, output location, and results.
7. Actual action distribution and selection-mode distribution, without treating them as targets.
8. Prediction, feedback, learning, and memory-coverage diagnostics.
9. Known limitations and any deviations requiring review.
10. Explicit confirmation that no held-out performance comparison was run.

## 8. Authorization

**Codex is authorized to implement the structured training mechanism in the local The Box repository, add tests, run the existing test suite, and run the bounded training-mechanics validation described above.**

This authorization does **not** include:
- changes to Box core or FiT world dynamics;
- changes to observation permissions or baselines;
- held-out evaluation or claims of beating the fixed-threshold baseline;
- overwriting historical experiment artifacts;
- uploading The Box source or raw experiment artifacts to this GitHub bridge.

If any of these boundaries must be crossed to complete the work, stop and request a separate decision.
