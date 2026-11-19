# Task 25: Bounded Training-Volume and Recurrence Diagnostic

**Status:** TRAINING-ONLY DIAGNOSTIC AUTHORIZED  
**Depends on:** Task 23 implementation; Task 24 coverage/generalization diagnostic  
**Repository:** Local The Box repository. GitHub bridge is coordination/reporting only.

## 1. Objective

Test whether increasing recurrence of the existing supported Task 23 scenario manifest materially improves exact observable-state/action coverage and the quality of learned transition estimates.

Task 24 found:
- 75 unique observable decision states and 91 state/action keys in the 96-decision Task 23 validation;
- 4 states with all three action estimates;
- repeated starting observations did acquire all three action estimates;
- most downstream states were visited only once;
- some repeated state/action keys produced different outcomes under different hidden condition/history contexts.

The next least-invasive hypothesis is that more training volume and recurrence may improve useful experience without changing the representation. This task tests that hypothesis. It is not a policy-performance evaluation.

## 2. Fixed design and boundaries

Use the existing Task 23 runner, exact observable-state/action key, agent policy, evaluator, learner, scenario constructors, and FiT dynamics unchanged.

Do not:
- change Box core or FiT world dynamics;
- change the observation interface, state/action key, learner, uncertainty policy, lexicographic comparison, or evaluator;
- introduce bins, state abstraction, nearest-neighbour generalization, or hidden-state features into the agent;
- impose action quotas, rotations, forced probes, or experimenter-selected actions;
- add counterfactual transition samples;
- change baselines or historical experiments;
- run held-out evaluation or claim performance improvement.

Experimenter-side condition/history fields may be used only to stratify analysis of outcome variation. They must not be passed to the agent, its key, selector, or learner.

If the existing runner cannot support the requested runs without source changes, stop and report the limitation. Do not modify it under this task.

## 3. Bounded run matrix

Use the existing eight-configuration manifest and run the following repetition levels with three predetermined seeds:

- repetitions: 2, 8, 16;
- seeds: 2501, 2502, 2503.

This is nine independent training runs. Each repetition level should preserve the same manifest construction and episode ordering used by Task 23. Each run starts with fresh empty agent memory and an independent seeded RNG. Do not carry memory between runs.

Expected run sizes, if the existing manifest remains eight configurations and six decisions per episode:

| Repetitions | Episodes per seed | Decisions per seed |
|---:|---:|---:|
| 2 | 16 | 96 |
| 8 | 64 | 384 |
| 16 | 128 | 768 |

Expected total: 208 episodes and 1,248 decisions per repetition level across the three seeds; 624 episodes and 3,744 decisions across all nine runs.

Use a unique, new output directory for every run, for example:
- experiments/results/task25-recurrence/r02-seed2501/
- experiments/results/task25-recurrence/r02-seed2502/
- experiments/results/task25-recurrence/r02-seed2503/
- corresponding r08-* and r16-* directories.

Before each run, verify that its output directory does not exist. Never overwrite. If any path exists, stop and report; do not delete or reuse it.

## 4. Required analysis

Report results per run, per repetition level, and across seeds. Keep the three seeds visible; do not hide instability behind a pooled average.

### 4.1 Coverage growth

Measure coverage as training progresses:
- unique decision-time observable states;
- distinct state/action keys;
- states with 1, 2, and 3 action estimates;
- fraction of states with complete three-action coverage;
- sample-count distribution for state/action keys;
- exact-state revisit rate;
- state/action revisit rate;
- initial-state coverage versus downstream-state coverage.

Report learning/coverage curves at episode checkpoints, not only final totals. Explain whether increased repetitions primarily:
- create new exact states;
- revisit and complete existing states;
- deepen existing state/action sample counts;
- or produce little marginal coverage improvement.

### 4.2 Selection and action diagnostics

Report actual action counts and proportions, plus uncertainty-directed, model-based, and tie-break selection modes. These are observations, not targets.

Verify that the policy and selection modes remain identical to Task 23. Do not interpret action proportions as evidence of decision quality.

### 4.3 Estimate reliability and hidden-context aliasing

For each repeated state/action key:
- report number of observations;
- report component means and variability for health, quality, and oxygen changes;
- identify keys whose observed component outcomes differ;
- stratify such differences by experimenter-side NORMAL/STRESSED condition and available history/recovery metadata;
- distinguish direct observation from possible causal explanation.

Keep agent-visible features separate from experimenter-only diagnostic strata. Do not modify the learner to account for hidden context.

Report prediction error using only predictions captured before execution and only where a valid observed outcome exists. Keep in-training prediction diagnostics clearly labelled; they are not held-out predictive-validity evidence.

### 4.4 INSPECT

Report how often INSPECT is selected, how often information is revealed, how many distinct inspection-result contexts occur, and whether those contexts increase key fragmentation or useful downstream differentiation.

Do not assign information a scalar reward.

### 4.5 Reproducibility

Confirm each run records the expected seed and configuration. If the existing runner supports deterministic repeatability checks without overwriting outputs, perform a non-mutating comparison of summaries/traces for a repeated same-seed run only if an already existing duplicate artifact is available. Otherwise, rely on Task 23's reproducibility tests and report that this task did not independently rerun identical seeds.

## 5. Stop conditions

Stop without running further jobs if:
- the runner's behavior or manifest differs materially from Task 23;
- any output path exists;
- a run would overwrite an artifact;
- source changes appear necessary;
- an unexpected error risks partial/corrupt output.

Report any completed runs and preserve their outputs. Do not clean up partial outputs.

## 6. Interpretation and decision

Conclude whether increased recurrence with the exact key appears to be:

**A. Promising:** complete coverage and sample depth improve materially and consistently across seeds, with tolerable within-key outcome variation.

**B. Insufficient:** coverage remains sparse or improves only for starting states while downstream exact-state coverage remains poor.

**C. Confounded:** more samples expose substantial outcome variation within identical agent-visible state/action keys, indicating that exact-key averages may be unreliable under hidden condition/history variation.

These categories may overlap; identify the dominant result and any secondary result.

Recommend the next design step:
- if A, propose a separate larger training/validation task, still before held-out evaluation;
- if B, propose a scenario-recurrence design review;
- if C, propose a representation/generalization design study that preserves the observation boundary and explicitly addresses partial observability. Do not add hidden variables to the agent.

No held-out performance comparison is authorized by this task.

## 7. Completion report

Append a report to this task document and push it to the bridge. Include:
1. Exact commands, seeds, repetition levels, and output directories.
2. Per-run episode/decision counts and completion status.
3. Coverage curves and final state/action coverage metrics.
4. Action and selection-mode distributions.
5. Repeated state/action sample-depth and component-variability analysis.
6. Prediction diagnostics with explicit limitations.
7. INSPECT/reveal/context-fragmentation analysis.
8. Reproducibility details.
9. Interpretation A/B/C and recommended next step.
10. Explicit confirmation that no source, tests, baselines, Box core, FiT dynamics, observation permissions, or historical artifacts were changed, and no held-out evaluation was run.

## 8. Authorization

Codex is authorized to run the nine bounded Task 25 training-only runs using the existing Task 23 implementation, write outputs only to the nine new non-existing directories specified above, and perform read-only analysis of those new outputs.

**No source changes, test changes, training-policy changes, representation changes, baseline changes, historical artifact writes, or held-out evaluation are authorized.**


## 9. Scale amendment: substantially increased training volume

**This section supersedes Section 3's original repetition matrix and the corresponding run-count figures.** All other Task 25 boundaries, runner requirements, analysis requirements, stop conditions, and interpretation rules remain in force.

The user has authorized a substantial increase in training volume before execution.

### Revised run matrix

Use repetition levels **16, 64, and 256**, each across seeds **2501, 2502, and 2503**.

Each repetition means one complete pass through the existing eight-configuration manifest, preserving Task 23's manifest construction and episode ordering. Each run starts with fresh empty memory and its own seeded RNG.

| Repetitions | Episodes per seed | Decisions per seed | Episodes across 3 seeds |
|---:|---:|---:|---:|
| 16 | 128 | 768 | 384 |
| 64 | 512 | 3,072 | 1,536 |
| 256 | 2,048 | 12,288 | 6,144 |
| **Total** | **2,688 per seed** | **16,128 per seed** | **8,064** |

**Grand total: 8,064 episodes and 48,384 decisions across nine independent runs.**

Use these unique output directories, verifying each does not exist before starting:
- `experiments/results/task25-recurrence/r16-seed2501/`
- `experiments/results/task25-recurrence/r16-seed2502/`
- `experiments/results/task25-recurrence/r16-seed2503/`
- `experiments/results/task25-recurrence/r64-seed2501/`
- `experiments/results/task25-recurrence/r64-seed2502/`
- `experiments/results/task25-recurrence/r64-seed2503/`
- `experiments/results/task25-recurrence/r256-seed2501/`
- `experiments/results/task25-recurrence/r256-seed2502/`
- `experiments/results/task25-recurrence/r256-seed2503/`

Never overwrite or reuse an existing output path. If any path exists, stop and report rather than deleting, renaming, or reusing it.

### Additional execution safeguards

- Before launching the full matrix, verify the runner's expected episode and decision counts using its existing interface or read-only inspection. Do not modify source to accommodate the larger run.
- Execute one run at a time and verify its completion and output integrity before proceeding to the next.
- Preserve partial outputs and stop on unexpected errors or resource problems. Do not silently reduce the requested scale.
- Keep progress summaries at episode checkpoints so coverage growth and diminishing returns can be assessed across the full run.
- If runtime or resource limits make the full matrix impractical, stop, report exactly what completed, and request a revised authorization. Do not substitute a smaller experiment without approval.

The larger scale does not authorize any change to the agent's algorithm, state/action key, observation boundary, FiT dynamics, scenario manifest, action-selection policy, or evaluator. It remains a training-only recurrence/volume diagnostic. **Held-out evaluation remains prohibited.**
