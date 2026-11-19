# Task 27: Held-Out Performance Evaluation of Task25-Trained Agents

**Status:** PERFORMANCE EVALUATION AUTHORIZED  
**Purpose:** Answer the central question: did the long Task25 training runs produce an agent that keeps more Fish health than the fixed-threshold baseline?

## 1. Objective

Evaluate the frozen Task23 agents produced by the three Task25 runs at 256 repetitions (seeds 2501, 2502, 2503) against the existing fixed-threshold baseline.

The primary outcome is **mean cumulative Fish health per episode**, using the established six-decision-cycle plus washout protocol. This is the decisive performance metric. Coverage, prediction error, action counts, and other diagnostics are secondary and must not substitute for it.

Task18/21 established that the original experience-enabled agent was substantially behind the fixed-threshold baseline. Task25 subsequently trained the Task23 agent for much longer, but did not evaluate held-out policy performance. Task27 must now measure that performance directly.

## 2. First: read-only feasibility and integrity check

Before running anything, inspect the existing Task25 outputs and current evaluation tooling.

For each 256-repetition seed (2501, 2502, 2503), establish whether the exact final learned memory/model snapshot from that run was preserved. Use the exact frozen end-of-training state, not a reconstruction from summary statistics and not a newly trained substitute.

Verify:
- the exact source run and seed;
- the 256-repetition training configuration and eight-entry manifest;
- the frozen memory/model snapshot identity and contents;
- that evaluation can load the snapshot without further learning;
- that the held-out evaluation scenarios do not overlap Task25 training configurations;
- that Task23's evaluator feedback, exploration/uncertainty-directed selection, and memory updates can all be disabled during evaluation;
- that the existing fixed-threshold baseline and experience-disabled control can be run unchanged.

If an exact frozen model snapshot is unavailable, or the current runner cannot perform a genuinely frozen evaluation without source changes, **stop and report the precise limitation**. Do not retrain, reconstruct, alter code, or silently substitute a different model. A separate implementation task can be considered if needed.

## 3. Evaluation protocol

Use the established Task18/Task21 evaluation conventions:
- six decision cycles plus the established washout;
- identical FiT dynamics and observation permissions;
- 300 distinct held-out scenario configurations;
- the same scenario configurations for every evaluated policy;
- identical episode initialization and matched random seeds/configuration inputs wherever supported;
- no evaluator feedback to the agent during evaluation;
- no learning or memory updates;
- no uncertainty-directed exploration;
- frozen Task25 memory for the entire evaluation.

Evaluate:
1. Task25 256-repetition agent, seed 2501.
2. Task25 256-repetition agent, seed 2502.
3. Task25 256-repetition agent, seed 2503.
4. Existing fixed-threshold baseline.
5. Existing experience-disabled control.

The three trained agents must remain separate. Do not pool their memories.

### Scenario selection

Prefer the exact Task21 300-scenario held-out manifest (scenario seed 211726) if inspection confirms it is valid for all three Task25 training runs and can be replayed under the current canonical protocol. This creates a direct matched comparison with Task21's fixed-threshold result.

If that manifest overlaps Task25 training configurations or is otherwise incompatible, generate one new manifest of 300 distinct configurations, held out from all Task25 training configurations, and use it identically across all five policies. Document the reason for not reusing Task21's manifest and record all seeds and manifest details.

Do not tune, filter, or select scenarios based on agent outcomes.

## 4. Primary analysis

For each of the five policies, report:
- mean cumulative Fish health per episode;
- standard deviation;
- median and interquartile range;
- per-scenario paired differences against the fixed-threshold baseline;
- number and proportion of scenarios where the policy scores higher, equal, or lower than the baseline;
- uncertainty intervals for the mean paired difference, using a clearly stated paired method.

For each Task25 seed, explicitly calculate:
- absolute difference from the fixed-threshold baseline;
- percentage difference relative to the baseline;
- paired scenario-level comparison;
- whether the result is consistently better/worse across NORMAL and STRESSED conditions.

Also report the fixed-threshold versus experience-disabled comparison as a control.

Keep seed-specific results visible. Do not hide variation across training seeds behind a pooled mean. If useful, additionally report a clearly labelled aggregate across the three independently trained agents.

## 5. Historical comparison

Place the new results beside the historical results:
- Task18 experience-enabled: use the exact reported value and label its evaluation context.
- Task21 experience-enabled: mean cumulative health 12.10.
- Task21 experience-disabled: 12.06.
- Task21 fixed-threshold: 26.22.

Do not imply that results from different scenario manifests are paired. Clearly distinguish:
1. direct within-Task27 matched comparisons;
2. descriptive comparison with historical Task18/Task21 results.

If Task18's exact outcome value differs from Task21's fresh result, preserve both and explain that they came from different evaluation runs/manifests.

## 6. Secondary diagnostics

Report, without letting them displace the primary outcome:
- action counts/proportions during evaluation;
- fraction of decisions using model-based selection versus fallback, if applicable;
- estimate coverage;
- episode completion and validity;
- any policy/runtime errors.

Verify evaluation freeze: no feedback, exploration, or learning occurred; the Task25 memory snapshots remained unchanged.

## 7. Artifacts and validation

Create new output directories only. Never overwrite Task18, Task21, or Task25 artifacts.

Preserve:
- evaluation scenario manifest;
- exact frozen model/memory identities or hashes;
- per-decision records;
- per-episode outcomes;
- per-scenario paired comparisons;
- machine-readable aggregate summary;
- human-readable report;
- tests or checks proving the frozen evaluation path did not mutate memory and all policies used the same scenario configurations.

Run the full existing test suite if no source changes are required and report the result. Do not modify source or tests under this task.

## 8. Boundaries

- No changes to Box core, FiT dynamics, Task23 agent, learner, selector, evaluator, observation interface, or baselines.
- No new training, tuning, exploration, or learning.
- No changes to historical artifacts.
- No hidden or experimenter-only information may be exposed to the agent.
- No counterfactual outcomes.
- No source or raw experiment artifacts uploaded to the bridge. Append only the concise report and artifact paths/identities.

## 9. Required conclusion

Answer plainly:

**Did any of the three Task25-trained agents beat the fixed-threshold baseline on held-out cumulative Fish health?**

If yes, state which seeds, by how much, and whether the improvement is consistent across conditions and scenarios.

If no, state how far behind they remain.

If evaluation cannot be run because the exact frozen models or a valid frozen evaluation path are unavailable, stop and report exactly what is missing. Do not replace the requested performance measurement with prediction accuracy, coverage, or training metrics.

No further task should be started automatically after reporting the result.


## Completion report — Task 27

Held-out evaluation completed on the exact Task21 manifest (seed 211726): 300 distinct scenarios, same configurations across five policies, six decisions plus washout. Exact Task25 snapshots were evaluated separately and remained unchanged. No Task25 training configuration overlapped the held-out manifest.

**Did any Task25 agent beat fixed threshold overall? No.** Fixed threshold mean cumulative health was 26.217. Task25 means were 12.653 (seed 2501; paired difference −13.563, −51.74%; 108 higher / 11 equal / 181 lower), 12.380 (seed 2502; −13.837, −52.78%; 108 / 12 / 180), and 12.660 (seed 2503; −13.557, −51.71%; 108 / 11 / 181). Their paired bootstrap 95% percentile CIs were [−16.170, −11.033], [−16.483, −11.260], and [−16.163, −11.033], respectively. All three were worse on NORMAL (142–143 lower, 2–3 equal, none higher) and had positive mean differences on STRESSED (each 108 higher), so performance was not consistent across conditions. Experience-disabled mean was 12.060; its paired difference from fixed was −14.157 (110 / 10 / 180; CI [−16.790, −11.457]). Task27 fixed-threshold mean (26.217) agrees with Task21's rounded 26.22. Task18's exact enabled mean was 12.503 on a separate manifest (seed 181726); historical values are descriptive, not paired.

Freeze checks: feedback observer absent for trained agents; frozen selector used without exploration or updates; ordinary agent inputs only; all 1,500 episodes valid; snapshot hashes unchanged. Full suite: 247 tests passed, zero failures, exit 0 (command: .\.venv\Scripts\python.exe -B -m unittest discover -s tests -v).

Local artifacts: experiments/results/task27-heldout-evaluation/ (scenario-manifest.json, decision-records.json, episode-outcomes.json, paired-scenario-comparisons.json, summary.json, report.md). Task25 memory SHA-256: seed 2501 e262261ffc817689ee3bd74a38fb0abd642ecb9f031e8f5a12da86a399c8ae29; seed 2502 b9749292801d2b752f342175f2b29e39353e704cadc41cd261977766e0f14a8d; seed 2503 55ba3d5188933eb0e90f50aa3160a2fc01aa5f3f935857833c996af64f1592cb.
