# Task 24: Diagnose Structured-Training Coverage and Generalization

**Status:** READ-ONLY DIAGNOSTIC AUTHORIZED  
**Depends on:** Task 23 implementation and bounded validation  
**Repository:** Local The Box repository. The GitHub bridge is for task/report coordination only.

## 1. Objective

Determine why the Task 23 agent used uncertainty-directed selection in 91 of 96 decisions, model-based selection in only 3, and tie-breaking in 2, while obtaining complete three-action estimates for only 4 of 75 observable states.

The immediate question is whether this is simply insufficient training volume, a training-manifest design issue, or a structural limitation of the exact observable-state/action representation.

Do not assume the answer. Inspect the actual implementation and existing local Task 23 artifacts.

The purpose is to decide what must change, if anything, before authorizing a held-out evaluation. Task 24 is diagnostic only. It is not a performance evaluation.

## 2. Strict read-only scope

- Do not modify source code, tests, configs, task runners, or experiment artifacts.
- Do not run training or evaluation experiments.
- Do not overwrite or regenerate any artifact.
- Do not change the state/action key, observation representation, selector, learner, scenario dynamics, or baselines.
- You may inspect source, tests, and existing Task 23 validation artifacts.
- You may run read-only analysis scripts/commands that load existing artifacts without writing files. If any command may write caches, logs, or outputs, do not run it unless you first report that risk.
- Do not upload source code or raw experiment artifacts to the bridge. Put only the diagnostic report here.

## 3. Questions to answer

### 3.1 Verify the reported coverage

Using the actual Task 23 code and existing Task 23 validation artifacts, independently verify:
- decision counts by selection mode;
- action counts;
- number of unique observable states;
- number of state/action keys;
- number of states with 0, 1, 2, or 3 action estimates;
- number of model-based decisions and tie-breaks;
- whether the reported 4 fully covered states and 75 visited states are correct.

Explain exactly how the observable-state key is constructed, including the role of quality, oxygen, health, and inspection result.

### 3.2 Diagnose why coverage is sparse

Trace the six-decision episode trajectories and determine:
- how often each starting configuration recurs;
- how often the same observable state recurs across episodes;
- whether action-dependent trajectories cause later states to diverge;
- whether INSPECT results further fragment the key space;
- whether the manifest repeats enough useful contexts to create complete action coverage;
- whether the selector's uncertainty rule itself contributes to sparse coverage.

Distinguish direct evidence from hypotheses.

### 3.3 Is more training volume sufficient?

Do not run new experiments. Use the existing traces and code to reason about the likely effect of increasing repetitions.

Where possible, provide descriptive calculations from existing data:
- exact-key revisit rates;
- observed transition/state branching;
- empirical number of visits per state;
- proportion of states with sufficient action coverage;
- whether existing repeated starting states accumulated all three action estimates.

State clearly what cannot be inferred from the small validation sample.

### 3.4 Assess representation alternatives without implementing them

Evaluate possible approaches, from least to most invasive:

1. More repetitions / longer training with the existing exact key.
2. More carefully repeated observable starting contexts using existing scenario constructors.
3. A coarser but still observation-valid state representation.
4. A structured generalization method over nearby observable states.

For each, explain:
- what it would solve;
- what information it would discard or blur;
- whether it risks merging states with materially different dynamics due to hidden history, condition, or delayed penalties;
- compatibility with the existing observation boundary;
- likely impact on prediction validity and interpretability;
- what tests and controls would be needed.

Do not silently choose or implement a new representation.

### 3.5 Inspect action-selection semantics

Verify that:
- uncertainty-directed selection is genuinely restricted to unestimated actions;
- it does not impose quotas or hidden balancing;
- model-based selection only occurs when the required estimates exist;
- selection-mode labels are correct;
- seeded reproducibility is properly implemented.

Identify whether the current policy's rule, “if any action is unestimated, choose among unestimated actions,” creates a starvation or coverage pathology. For example, determine whether a state with two known actions and one unknown action always selects the unknown action, and what this implies for the other action estimates and future visits.

Do not recommend changing this rule without explaining the consequences.

### 3.6 Recommendation

Conclude with one of:
- **A.** Existing representation is adequate; increase training volume / improve manifest and then validate coverage.
- **B.** Exact-key representation is too sparse; a new representation/generalization design is required before further training.
- **C.** Evidence is insufficient; specify the smallest additional read-only diagnostic needed.

Provide a recommended next step, the evidence supporting it, risks, and the exact decisions that would require owner approval.

## 4. Required report

Append a report to this task document containing:
1. Executive verdict.
2. Verified source paths and symbols.
3. Independently verified artifact metrics.
4. State visitation and trajectory analysis.
5. Training-volume assessment.
6. Representation alternatives and trade-offs.
7. Selector semantics review.
8. Recommendation A/B/C.
9. Explicit confirmation that no files, tests, configurations, experiments, or artifacts were changed.

Do not propose held-out performance evaluation until the coverage/generalization question is resolved. Do not implement anything.

## 5. Authorization

Codex is authorized to inspect the local implementation and existing Task 23 validation artifacts and to perform non-writing analysis only.

**No source changes, tests, training runs, held-out evaluation, or artifact writes are authorized by Task 24.**


---

## Diagnostic report (Codex)

### 1. Executive verdict

**Recommendation C: the existing evidence is insufficient to decide whether more training volume alone will solve the coverage problem or whether the exact observable-state table needs a generalization design.** The artifact independently confirms 75 distinct decision-time observable states, 91 distinct state/action estimates, and only 4 states with all three actions estimated. Repeated starting contexts did acquire full action coverage, so repetition can help where exact contexts recur. Downstream states mostly did not recur, and the short, action-dependent trajectories do not establish what a longer run would do.

The traces also show limited evidence of aliasing: two of five repeated state/action keys had different observed outcomes. Those differences are compatible with condition/history dependence omitted from the agent key, but the sample is too small to establish how broadly that affects predictions. No held-out or other performance evaluation was performed.

### 2. Source and artifact evidence checked

Read-only inspection covered:

- `experiments/fit_task23_structured_training.py`: `_scenario_manifest`, `_training_policy`, `run_training`, `DecisionTrace`, `freeze_estimates`, and `select_frozen_evaluation_action`.
- `experiments/fit_task9_experience.py`: `StateActionKey`, `state_action_key`, `TransitionEstimate`, and `update_transition_estimate`.
- `experiments/fit_task6_agent.py`: `FitTask6Agent.start_episode` and `step`, including observation and inspection-result timing.
- `experiments/fit_task6_environment.py` and `experiments/fit_task6_calculation.py`: current condition/history ownership and committed cycle records.
- The Task 23 tests were read but not run.
- Existing local artifacts: `experiments/results/task23-training-validation-complete2/{scenario-manifest.json,training-traces.json,training-memory.json,summary.json}`.

The key is `(quality, oxygen, health, inspection_result, action)`. The state portion therefore uses the agent's three numeric observations and its inspection result if one has already been revealed. It does not include the hidden condition before inspection, adverse-history counters, recovery progress, pending delayed penalties, episode/cycle, or scenario identity. The fourth field distinguishes `None`, `NORMAL`, and `STRESSED` inspection contexts.

### 3. Independently verified artifact metrics

I parsed the existing JSON files with read-only PowerShell `Get-Content` / `ConvertFrom-Json` commands and recomputed groupings from the traces and memory; I did not run Python, tests, training, or evaluation.

| Metric | Recomputed result |
|---|---:|
| Episodes / decisions / actual updates | 16 / 96 / 96 |
| Selection modes | uncertainty-directed 91; model-based 3; tie-break 2 |
| Actions | WAIT 34; INSPECT 29; MAINTAIN 33 |
| Unique observed decision states | 75 |
| Distinct state/action estimate keys | 91 |
| Estimate-key sample counts | 86 keys seen once; 5 seen twice |
| Visited states with 0 / 1 / 2 / 3 action estimates | 0 / 63 / 8 / 4 |
| State/action keys by action | WAIT 32; INSPECT 28; MAINTAIN 31 |

Thus the headline 75 visited states and 4 completely covered states are correct. All 75 visited decision states have at least one estimate because each recorded decision updates the actually selected action. The artifact's 91 keys sum to 96 observations.

### 4. Visitation, manifest, and trajectory findings

The manifest has eight exact scenario configurations (four initial Tank/Fish states in each of NORMAL and STRESSED); each scenario ID occurs twice. Because the observation key omits condition/history and inspection is reset at episode start, the four distinct initial observable states each occur four times across the 16 episodes: twice under each condition. All four initial states ended with estimates for all three actions:

- (3, 6, 3): INSPECT 1, MAINTAIN 2, WAIT 1 observations.
- (5, 5, 6): INSPECT 1, MAINTAIN 2, WAIT 1.
- (7, 4, 8): INSPECT 1, MAINTAIN 1, WAIT 2.
- (9, 9, 5): INSPECT 2, MAINTAIN 1, WAIT 2.

This supports a limited claim: recurrence of an exact start observation let the uncertainty policy fill its three action slots. It does not show that two repetitions of each exact condition/history configuration suffice on their own; the key pools occurrences across conditions.

Across 96 decisions there were 75 unique states and 21 visits beyond each state's first visit: 18 cross-episode revisit events and 3 repeats within an episode. Ten states appeared in more than one episode. The visitation-frequency distribution was 63 states visited once, 8 visited twice, 3 visited four times, and 1 visited five times.

Each of the eight same-scenario episode pairs had a different first action on its second occurrence. Their later trajectories also differ. This is direct evidence that the repeated episodes were not matched trajectories; the second occurrence followed a different position in the single seeded RNG stream and a different learned memory. Action-dependent transitions, hidden condition/history, and evolving selection state all complicate attribution. It is not evidence that increasing repetitions will necessarily reproduce either trajectory.

Cumulative coverage by episode shows the first eight episodes introduced 42 states and no complete states. By the end of episode 11, the four repeated initial observable states were fully covered. The last four episodes introduced 17 additional states but no additional fully covered states. Five model-based/tie-break decisions occurred after full initial-state coverage began. At those covered states, further revisits choose according to the model (or tie-break); they do not continue exploring the already estimated alternatives, so additional repetitions may increase samples for selected actions without increasing all-action coverage.

INSPECT appears in 29 decisions. The trace contains 29 subsequent decisions with a revealed result available. Of the 75 observable states, 29 have no inspection result, 24 have NORMAL revealed, and 22 have STRESSED revealed. Seventeen physical `(quality, oxygen, health)` triples appear in more than one inspection-result partition. This split is consistent with the current key: revealed information creates a different agent-observable context. It is not a hidden-state leak.

### 5. Volume and hidden-history assessment

The current sample is small. It shows that additional visits to the same initial observation can fill missing action estimates; it does not estimate the volume needed for broad coverage. Most downstream exact observations occurred once, and 63/75 visited states still have only one action estimate. Only four states have all three. There is no sound extrapolation from these 16 episodes to a training duration that would cover enough downstream states.

There are five repeated state/action keys. Two had different observed component deltas:

- `(3,6,3,None,MAINTAIN)`: NORMAL produced `(quality +1, oxygen +1, health -2)`; STRESSED produced `(+1,+1,-3)`, with the same initial history counters.
- `(5,5,6,None,MAINTAIN)`: NORMAL produced `(+1,+1,+1)`; STRESSED produced `(+1,+1,-5)`, also with the same initial history counters.

A third differing repeat, `(9,9,5,None,WAIT)`, had different recovery-progress contexts and different health changes. These are direct observed examples of contexts sharing the current key but not the same outcome. They are consistent with the documented hidden condition/history mechanics; with only two samples per repeated key, they do not quantify general predictive error or establish which omitted factor caused each difference.

The policy updates only from the selected, actually executed transition. It has no counterfactual observations. The 288 configured candidate slots (96 decisions × 3 actions) must not be mistaken for 288 transition samples; there were 96 actual updates.

### 6. Selector semantics

Source inspection confirms:

- If at least one action has no exact-key estimate, `_training_policy` selects only from the missing-action set using one sequential `random.Random(seed)` stream.
- It does not impose quotas, balance action frequencies, rotate actions, or force probes.
- Model comparison occurs only if all three exact-key estimates exist. The comparison tuple is `(mean_health_change, mean_quality_change, mean_oxygen_change)`, so it is lexicographic and not scalarized.
- Full-vector ties use the seeded RNG and are labeled `tie_break`; decisions with incomplete estimates are labeled `uncertainty_directed`; unique full-vector winners are labeled `model_based`.
- The trace records candidate estimate availability, chosen action, mode, and the overall artifact records the seed and RNG type. The test source includes missing-action, lexicographic, tie, and same-seed reproducibility checks. I did not execute those tests under this read-only task.

The missing-action rule does not starve the unknown action: if two actions are known and one is missing, only that missing action can be selected. Once it is learned, the state becomes eligible for model selection. The rule also does not sample already estimated alternatives during uncertainty-directed selection. After all actions are known, selection is exploitative except for tied vectors. This is consistent with the intended training policy, but it means coverage counts do not imply well-sampled estimates: the four complete start states have only one or two observations per action.

### 7. Representation alternatives

1. **More repetitions / longer training with the exact key.** This is the least invasive and can fill repeatedly visited keys, as the four initial states demonstrate. It preserves interpretability and the observation boundary. It may mostly deepen estimates for model-selected actions after a state becomes complete; it cannot guarantee downstream revisits. It does not resolve hidden condition/history variation within a key.

2. **More repeated observable starts with existing scenario constructors.** This could deliberately create more recurrence at supported states and improve their action coverage without forcing actions or changing dynamics. It would clarify those contexts, but could overrepresent starts, may still yield divergent downstream states, and pools conditions/history whenever the agent cannot observe them. Report coverage by configuration and by agent-observable key separately.

3. **A coarser observation-valid state representation.** Binning quality/oxygen/health could reduce sparsity while using no hidden information. It would blur states around the health threshold, clipping boundaries, and INSPECT reveal partitions, potentially mixing materially different transition behavior. This would reduce table transparency and could lower predictive validity. It requires owner approval plus boundary-focused prediction tests and explicit comparison of coverage versus error.

4. **Structured generalization over nearby observable states.** A model using only permitted observations could share evidence among related states more flexibly than bins. It would add design and tuning complexity, and may interpolate across threshold/clipping regimes or contexts with different hidden histories/conditions. It needs a declared feature/generalization rule, calibration and error diagnostics by observable region and experimenter-only condition/history strata, and tests preventing hidden inputs from reaching the agent model. No such method should be selected implicitly.

Neither coarsening nor generalization can legitimately add hidden condition/history to the agent key without a separate observation-permission decision. Since the task is not authorized to change representation, these are design options only.

### 8. Recommendation and owner decisions

**C. Evidence is insufficient.** The smallest useful read-only diagnostic has been completed here: a per-state visitation/action-coverage ledger and repeated state/action outcome comparison derived from the existing Task 23 traces. No additional read-only operation on these same artifacts can establish whether a longer training run would be sufficient. Answering that requires a separately authorized, bounded training-only follow-up with coverage growth reported by episode and no held-out comparison.

Before that follow-up, the owner should decide whether to retain the exact observation-valid key and test increased recurrence/volume, or commission a representation/generalization design. A representation change, altered observation boundary, or held-out performance evaluation each requires separate explicit authorization. Do not proceed to held-out evaluation on the current coverage evidence.

### 9. Read-only confirmation

No local source, test, configuration, training/evaluation artifact, or result was changed. No tests, training, or evaluation were run. The only write for this task is this diagnostic report appended to the authorized GitHub task document; no source or raw experiment artifact was uploaded.
