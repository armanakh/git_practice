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


---

## Completion report (Codex)

### 1. Executive result

**Interpretation: C (confounded) is dominant; B (insufficient coverage) is a substantial secondary result.** Recurrence increased complete-state coverage from 64–66 states at 16 repetitions to 74–77 at 64, but by 256 repetitions the result was 74–78. Unique states and keys also largely plateaued by 64–128 repetitions. At the same time, repeated exact state/action keys continued to produce different Fish-health outcomes across hidden condition/history strata. The result does not justify held-out evaluation.

All nine authorized runs completed. Total: 8,064 episodes and 48,384 decisions. Every run produced and passed read-only parsing/count checks for its four expected JSON artifacts. No stop condition occurred.

### 2. Exact commands, seeds, and outputs

The runner invocation for every run was:

    .\.venv\Scripts\python.exe -B -m experiments.fit_task23_structured_training --seed SEED --repetitions REPETITIONS --output-dir experiments/results/task25-recurrence/DIRECTORY

The actual seed/repetition/output values were:

| Repetitions | Seed | Episodes | Decisions | Output directory |
|---:|---:|---:|---:|---|
| 16 | 2501 | 128 | 768 | experiments/results/task25-recurrence/r16-seed2501/ |
| 16 | 2502 | 128 | 768 | experiments/results/task25-recurrence/r16-seed2502/ |
| 16 | 2503 | 128 | 768 | experiments/results/task25-recurrence/r16-seed2503/ |
| 64 | 2501 | 512 | 3,072 | experiments/results/task25-recurrence/r64-seed2501/ |
| 64 | 2502 | 512 | 3,072 | experiments/results/task25-recurrence/r64-seed2502/ |
| 64 | 2503 | 512 | 3,072 | experiments/results/task25-recurrence/r64-seed2503/ |
| 256 | 2501 | 2,048 | 12,288 | experiments/results/task25-recurrence/r256-seed2501/ |
| 256 | 2502 | 2,048 | 12,288 | experiments/results/task25-recurrence/r256-seed2502/ |
| 256 | 2503 | 2,048 | 12,288 | experiments/results/task25-recurrence/r256-seed2503/ |

Before the matrix and immediately before each run, the requested output path was checked and found absent. Runs were executed sequentially. The same eight scenario IDs and manifest contents were verified in all nine output directories. Each run had the expected seed, episode count, decision count, 8 manifest entries, 4 JSON files, and matching trace episode count. The Python -B flag suppressed bytecode cache writes.

### 3. Coverage by run

Coverage is recomputed from decisions and actually executed actions. State revisits are the fraction of decisions after a state's first visit; state/action revisits use the same definition for each state/action pair. Coverage columns show states with 1, 2, or 3 action estimates; no visited state had zero.

| Reps | Seed | States / keys | States with 1 / 2 / 3 actions | State / pair revisit rate |
|---:|---:|---:|---:|---:|
| 16 | 2501 | 160 / 301 | 83 / 13 / 64 | 79.2% / 60.8% |
| 16 | 2502 | 148 / 301 | 61 / 21 / 66 | 80.7% / 60.8% |
| 16 | 2503 | 156 / 302 | 74 / 18 / 64 | 79.7% / 60.7% |
| 64 | 2501 | 167 / 331 | 80 / 10 / 77 | 94.6% / 89.2% |
| 64 | 2502 | 150 / 316 | 58 / 18 / 74 | 95.1% / 89.7% |
| 64 | 2503 | 157 / 320 | 71 / 9 / 77 | 94.9% / 89.6% |
| 256 | 2501 | 167 / 333 | 79 / 10 / 78 | 98.6% / 97.3% |
| 256 | 2502 | 150 / 316 | 58 / 18 / 74 | 98.8% / 97.4% |
| 256 | 2503 | 157 / 320 | 71 / 9 / 77 | 98.7% / 97.4% |

All four distinct initial observable states had all three action estimates in every run. After removing those four starts, downstream unique-state counts were 144–163 at 16 repetitions, 146–163 at 64, and 146–163 at 256. Downstream coverage remained mixed: 58–83 states had only one action, 9–21 had two, and 60–74 had all three, depending on run. Thus the result is not limited to start states, but many downstream states remain incomplete.

### 4. Coverage curves

Each checkpoint is after a complete eight-episode pass through the manifest. Values are unique states / state-action keys / fully covered states. These were derived post-run from the saved traces, so the full curves remain auditable without adding runner instrumentation.

| Seed | Pass 1 | Pass 2 | Pass 4 | Pass 8 | Pass 16 | Pass 32 | Pass 64 | Pass 128 | Pass 256 |
|---:|---|---|---|---|---|---|---|---|---|
| 2501 | 40/48/1 | 71/90/6 | 108/161/20 | 150/260/46 | 160/301/64 | 162/311/69 | 167/331/77 | 167/333/78 | 167/333/78 |
| 2502 | 38/47/1 | 71/90/5 | 109/164/18 | 137/247/42 | 148/301/66 | 150/316/74 | 150/316/74 | 150/316/74 | 150/316/74 |
| 2503 | 41/47/1 | 75/90/4 | 107/161/21 | 145/250/45 | 156/302/64 | 157/319/76 | 157/320/77 | 157/320/77 | 157/320/77 |

Most unique-state/key growth occurs before pass 32. Seed 2502 is flat after pass 32; seed 2503 is effectively flat after pass 64; seed 2501 gains only two keys and one fully covered state between passes 64 and 128, then is flat through pass 256. The number of fully covered states is about 40–45% of observed states at 16 repetitions and 46–49% at 64–256.

### 5. Actions, selection, sample depth, and predictions

Counts are ordered WAIT / INSPECT / MAINTAIN and uncertainty-directed / model-based / tie-break. Parenthesized percentages are action proportions within that run.

| Reps | Seed | Actions: count (proportion) | Selection modes |
|---:|---:|---|---|
| 16 | 2501 | 175 (22.8%) / 163 (21.2%) / 430 (56.0%) | 301 / 348 / 119 |
| 16 | 2502 | 183 (23.8%) / 213 (27.7%) / 372 (48.4%) | 301 / 309 / 158 |
| 16 | 2503 | 170 (22.1%) / 167 (21.7%) / 431 (56.1%) | 302 / 369 / 97 |
| 64 | 2501 | 496 (16.1%) / 437 (14.2%) / 2,139 (69.6%) | 331 / 2,097 / 644 |
| 64 | 2502 | 515 (16.8%) / 689 (22.4%) / 1,868 (60.8%) | 316 / 1,943 / 813 |
| 64 | 2503 | 512 (16.7%) / 427 (13.9%) / 2,133 (69.4%) | 320 / 2,256 / 496 |
| 256 | 2501 | 1,737 (14.1%) / 1,516 (12.3%) / 9,035 (73.5%) | 333 / 9,211 / 2,744 |
| 256 | 2502 | 1,844 (15.0%) / 2,573 (20.9%) / 7,871 (64.1%) | 316 / 8,522 / 3,450 |
| 256 | 2503 | 1,867 (15.2%) / 1,425 (11.6%) / 8,996 (73.2%) | 320 / 9,906 / 2,062 |

Action proportions are descriptive and are not targets or evidence of decision quality. Source inspection confirms the Task 23 rule remained unchanged: uncertainty selection draws only from actions missing at the current exact key; model selection requires all three estimates; full-vector ties use the seeded RNG. No quotas or balancing were introduced. In every run, uncertainty-selection count equals the number of distinct state/action keys: each new pair first receives one actual selected transition. As state coverage accumulated, model-based choices dominated. Further recurrence then deepened samples for chosen actions rather than refreshing every alternative.

For each run, memory-key sample counts were grouped as 1 / 2 / 3 / 4+ observations:

| Reps | Seed 2501 | Seed 2502 | Seed 2503 |
|---:|---|---|---|
| 16 | 233 / 7 / 13 / 48 | 225 / 12 / 8 / 56 | 235 / 14 / 5 / 48 |
| 64 | 249 / 3 / 7 / 72 | 231 / 4 / 3 / 78 | 241 / 4 / 1 / 74 |
| 256 | 250 / 3 / 4 / 76 | 231 / 4 / 3 / 78 | 241 / 4 / 1 / 74 |

Maximum single-key depth grew from 31 observations at 16 repetitions, to 127 at 64, and 511 at 256. This depth was concentrated in a small number of keys while hundreds of keys remained singletons.

Pre-execution selected-prediction diagnostics (health / quality / oxygen MAE) were:

| Reps | Seed 2501: n / MAE | Seed 2502: n / MAE | Seed 2503: n / MAE |
|---:|---|---|---|
| 16 | 467 / 0.460 / 0 / 0 | 467 / 0.256 / 0 / 0 | 466 / 0.464 / 0 / 0 |
| 64 | 2,741 / 0.322 / 0 / 0 | 2,756 / 0.160 / 0 / 0 | 2,752 / 0.314 / 0 / 0 |
| 256 | 11,955 / 0.298 / 0 / 0 | 11,972 / 0.142 / 0 / 0 | 11,968 / 0.282 / 0 / 0 |

These are selected-action, in-training errors against transitions also used for learning. They are not held-out predictive-validity results. Zero quality/oxygen MAE is consistent with those component transitions being determined by observable Tank state and action in the current mechanics; health error remains nonzero and is confounded by hidden context.

### 6. Repeated state/action outcomes and hidden-context aliasing

Repeated-key analysis grouped all actual feedback records by the exact agent-visible state/action key. Across runs, 67–85 keys had at least two observations; 7–13 of those keys had differing outcome vectors. Every observed within-key difference was in Fish health: quality and oxygen deltas had zero within-key variability in all runs. The numbers of repeated groups spanning both experimenter conditions ranged from 12 to 20; groups spanning different recorded adverse/maintenance/recovery tuples ranged from 8 to 13. Among the outcome-varying keys, 2–9 per run crossed conditions and 2–7 crossed history tuples; 2–4 per run varied even within an identical condition/history-counter stratum. Those remaining differences could involve unrecorded pending-penalty/action-history context; the traces do not justify causal attribution.

The tables below enumerate every state/action key that repeated in at least one tier for each seed. For each tier they give observation count, mean Q/O/H delta, and population SD Q/O/H. The final column stratifies health differences at 256 repetitions by experimenter condition and the available pre-cycle history tuple (adverse, maintenance-adverse, recovery). It is diagnostic-only; these hidden fields were not passed to the agent. “Constant” means observed vectors were identical within that key at the 256-repetition endpoint. A sample SD of zero at n=1 is merely a one-observation description.

### Per-key repeated outcome table — seed 2501

| key (q,o,h,inspection,action) | r16 n;mean Q/O/H;SD Q/O/H | r64 n;mean Q/O/H;SD Q/O/H | r256 n;mean Q/O/H;SD Q/O/H | r256 variable-health strata |
|---|---|---|---|---|
| (1,4,0,NORMAL,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (2,5,0,NORMAL,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (2,5,0,STRESSED,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (2,5,1,-,MAINTAIN) | 18;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 66;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 270;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (2,5,1,NORMAL,MAINTAIN) | 4;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 28;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 114;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (2,5,1,STRESSED,MAINTAIN) | 3;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 27;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 121;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (3,6,0,-,MAINTAIN) | 18;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 66;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 270;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (3,6,0,NORMAL,MAINTAIN) | 5;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 29;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 115;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (3,6,0,STRESSED,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 27;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 121;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (3,6,3,-,INSPECT) | 11;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 59;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 239;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (3,6,3,-,WAIT) | 20;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 68;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 272;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (4,4,0,STRESSED,MAINTAIN) | 3;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (4,7,0,-,MAINTAIN) | 19;m=1.00,1.00,0.53;sd=0.00,0.00,0.50 | 67;m=1.00,1.00,0.51;sd=0.00,0.00,0.50 | 271;m=1.00,1.00,0.52;sd=0.00,0.00,0.50 | NORMAL (2, 1, 0):H 1.00 [1..1]; NORMAL (3, 1, 0):H 1.00 [1..1]; STRESSED (2, 1, 0):H 0.01 [0..1] |
| (4,7,0,NORMAL,MAINTAIN) | 6;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 30;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 116;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (4,7,0,STRESSED,MAINTAIN) | 3;m=1.00,1.00,0.33;sd=0.00,0.00,0.47 | 28;m=1.00,1.00,0.07;sd=0.00,0.00,0.26 | 122;m=1.00,1.00,0.02;sd=0.00,0.00,0.13 | STRESSED (2, 1, 0):H 0.02 [0..1] |
| (5,5,1,STRESSED,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,5,6,-,MAINTAIN) | 31;m=1.00,1.00,-2.00;sd=0.00,0.00,2.95 | 127;m=1.00,1.00,-2.00;sd=0.00,0.00,2.99 | 511;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | NORMAL (1, 1, 1):H 1.00 [1..1]; STRESSED (1, 0, 1):H -2.00 [-2..-2]; STRESSED (1, 1, 1):H -5.00 [-5..-5] |
| (5,5,8,-,MAINTAIN) | na | 4;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 4;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (5,5,8,NORMAL,MAINTAIN) | na | 4;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 31;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (5,5,9,-,MAINTAIN) | na | 3;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 17;m=1.00,1.00,-0.41;sd=0.00,0.00,1.50 | NORMAL (0, 0, 1):H 1.00 [1..1]; STRESSED (0, 0, 1):H -2.00 [-2..-2] |
| (5,5,9,NORMAL,MAINTAIN) | 1;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 13;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (5,5,9,STRESSED,MAINTAIN) | 8;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 52;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 239;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (5,8,0,-,MAINTAIN) | 7;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 31;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 129;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,8,0,STRESSED,MAINTAIN) | 1;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 24;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 118;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,8,1,-,MAINTAIN) | 5;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 29;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 135;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (5,8,1,NORMAL,MAINTAIN) | 1;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 25;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 111;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,1,-,MAINTAIN) | 13;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,7,-,INSPECT) | 1;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 7;m=-1.00,-1.00,0.86;sd=0.00,0.00,0.35 | 35;m=-1.00,-1.00,0.94;sd=0.00,0.00,0.23 | NORMAL (0, 0, 0):H 1.00 [1..1]; STRESSED (1, 1, 0):H 0.00 [0..0] |
| (6,6,7,-,MAINTAIN) | 13;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 52;m=1.00,1.00,0.87;sd=0.00,0.00,0.96 | 221;m=1.00,1.00,0.84;sd=0.00,0.00,1.04 | NORMAL (0, 0, 0):H 1.00 [1..1]; STRESSED (1, 1, 0):H -6.00 [-6..-6] |
| (6,6,7,-,WAIT) | 1;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 7;m=-1.00,-1.00,0.86;sd=0.00,0.00,0.35 | 7;m=-1.00,-1.00,0.86;sd=0.00,0.00,0.35 | NORMAL (0, 0, 0):H 1.00 [1..1]; STRESSED (1, 1, 0):H 0.00 [0..0] |
| (6,6,7,STRESSED,INSPECT) | 5;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 28;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 118;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,6,7,STRESSED,WAIT) | 2;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 23;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 120;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,-,INSPECT) | 1;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 9;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 36;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,-,WAIT) | 1;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 19;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,NORMAL,MAINTAIN) | 3;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 8;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 37;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,STRESSED,INSPECT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 21;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 99;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,STRESSED,WAIT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 27;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 119;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,9,-,MAINTAIN) | 1;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,9,NORMAL,MAINTAIN) | na | 3;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 30;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,10,-,MAINTAIN) | na | na | 8;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,6,10,NORMAL,MAINTAIN) | 5;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 7;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 17;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,9,0,-,MAINTAIN) | 5;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 29;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 127;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,9,0,STRESSED,MAINTAIN) | na | 22;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 116;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,9,2,-,MAINTAIN) | 3;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 27;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 133;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,9,2,NORMAL,MAINTAIN) | 1;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 23;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 109;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,4,8,-,MAINTAIN) | 30;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | 126;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | 510;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | NORMAL (2, 1, 0):H 1.00 [1..1]; STRESSED (2, 1, 0):H -5.00 [-5..-5] |
| (7,7,0,-,MAINTAIN) | 20;m=1.00,1.00,0.45;sd=0.00,0.00,0.50 | 116;m=1.00,1.00,0.49;sd=0.00,0.00,0.50 | 500;m=1.00,1.00,0.50;sd=0.00,0.00,0.50 | STRESSED (3, 3, 0):H 0.50 [0..1] |
| (7,7,7,-,INSPECT) | 3;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 13;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 62;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,-,MAINTAIN) | 2;m=1.00,1.00,-0.50;sd=0.00,0.00,1.50 | 2;m=1.00,1.00,-0.50;sd=0.00,0.00,1.50 | 2;m=1.00,1.00,-0.50;sd=0.00,0.00,1.50 | NORMAL (0, 0, 1):H 1.00 [1..1]; STRESSED (0, 0, 1):H -2.00 [-2..-2] |
| (7,7,7,-,WAIT) | 2;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 15;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 56;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,NORMAL,MAINTAIN) | 8;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 49;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 193;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,STRESSED,INSPECT) | 6;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 20;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 97;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,STRESSED,WAIT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 23;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 96;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,8,-,MAINTAIN) | 11;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 49;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 214;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,9,NORMAL,MAINTAIN) | 9;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 54;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 227;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,10,-,MAINTAIN) | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 24;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 114;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (7,7,10,NORMAL,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 26;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 128;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (8,5,3,-,MAINTAIN) | 13;m=1.00,1.00,-3.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,-3.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,-3.00;sd=0.00,0.00,0.00 | constant |
| (8,5,9,-,MAINTAIN) | 13;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,0,-,WAIT) | 10;m=-1.00,-1.00,0.10;sd=0.00,0.00,0.30 | 58;m=-1.00,-1.00,0.02;sd=0.00,0.00,0.13 | 250;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.06 | STRESSED (3, 3, 0):H 0.00 [0..1] |
| (8,8,1,-,MAINTAIN) | 7;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 55;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 247;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,-,INSPECT) | 8;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 33;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 128;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,-,WAIT) | 7;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 30;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 120;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,NORMAL,INSPECT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 22;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 74;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,NORMAL,WAIT) | 3;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 14;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 64;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,STRESSED,INSPECT) | 3;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 13;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 59;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,STRESSED,WAIT) | 3;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 13;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 64;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,8,NORMAL,INSPECT) | 2;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 21;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 104;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,8,NORMAL,WAIT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 27;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 88;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,9,-,INSPECT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 25;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 100;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,9,-,WAIT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 23;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 113;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,10,-,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 22;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 112;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (8,8,10,NORMAL,MAINTAIN) | 10;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 78;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 353;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,6,0,-,MAINTAIN) | 11;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 59;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 251;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,6,10,-,MAINTAIN) | 11;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 59;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 251;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,7,0,-,MAINTAIN) | 9;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 57;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 249;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,7,10,-,MAINTAIN) | 9;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 57;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 249;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,8,0,-,MAINTAIN) | 7;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 55;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 247;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,8,10,-,MAINTAIN) | 7;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 55;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 247;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,9,0,-,MAINTAIN) | 8;m=0.00,0.00,0.00;sd=0.00,0.00,0.00 | 56;m=0.00,0.00,0.00;sd=0.00,0.00,0.00 | 248;m=0.00,0.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,9,5,-,INSPECT) | 15;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 63;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 262;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (9,9,5,-,WAIT) | 16;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 64;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 249;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (9,9,10,-,MAINTAIN) | 5;m=0.00,0.00,-1.00;sd=0.00,0.00,0.00 | 53;m=0.00,0.00,-1.00;sd=0.00,0.00,0.00 | 245;m=0.00,0.00,-1.00;sd=0.00,0.00,0.00 | constant |

### Per-key repeated outcome table — seed 2502

| key (q,o,h,inspection,action) | r16 n;mean Q/O/H;SD Q/O/H | r64 n;mean Q/O/H;SD Q/O/H | r256 n;mean Q/O/H;SD Q/O/H | r256 variable-health strata |
|---|---|---|---|---|
| (1,4,0,STRESSED,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (2,5,0,NORMAL,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (2,5,0,STRESSED,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (2,5,1,-,MAINTAIN) | 16;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (2,5,1,NORMAL,MAINTAIN) | 4;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 26;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 118;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (2,5,1,STRESSED,MAINTAIN) | 5;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 34;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 134;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (3,6,0,-,MAINTAIN) | 16;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (3,6,0,NORMAL,MAINTAIN) | 5;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 27;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 119;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (3,6,0,STRESSED,MAINTAIN) | 7;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 36;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 136;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (3,6,3,-,INSPECT) | 13;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 64;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 256;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (3,6,3,-,WAIT) | 18;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 63;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 255;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (4,1,2,-,MAINTAIN) | 2;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 14;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 63;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (4,1,2,NORMAL,MAINTAIN) | 2;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 6;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 32;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (4,1,2,STRESSED,MAINTAIN) | 5;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 46;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 214;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (4,4,0,STRESSED,MAINTAIN) | 3;m=1.00,1.00,0.67;sd=0.00,0.00,0.47 | 3;m=1.00,1.00,0.67;sd=0.00,0.00,0.47 | 3;m=1.00,1.00,0.67;sd=0.00,0.00,0.47 | STRESSED (3, 3, 0):H 1.00 [1..1]; STRESSED (4, 2, 0):H 0.00 [0..0]; STRESSED (4, 4, 0):H 1.00 [1..1] |
| (4,7,0,-,MAINTAIN) | 17;m=1.00,1.00,0.65;sd=0.00,0.00,0.48 | 62;m=1.00,1.00,0.60;sd=0.00,0.00,0.49 | 254;m=1.00,1.00,0.54;sd=0.00,0.00,0.50 | NORMAL (2, 1, 0):H 1.00 [1..1]; NORMAL (3, 1, 0):H 1.00 [1..1]; STRESSED (2, 1, 0):H 0.01 [0..1] |
| (4,7,0,NORMAL,MAINTAIN) | 6;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 28;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 120;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (4,7,0,STRESSED,MAINTAIN) | 8;m=1.00,1.00,0.38;sd=0.00,0.00,0.48 | 37;m=1.00,1.00,0.08;sd=0.00,0.00,0.27 | 137;m=1.00,1.00,0.02;sd=0.00,0.00,0.15 | STRESSED (2, 0, 0):H 0.00 [0..0]; STRESSED (2, 1, 0):H 0.02 [0..1] |
| (5,2,0,-,MAINTAIN) | 1;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 12;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,2,0,NORMAL,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 7;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 33;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,2,0,STRESSED,MAINTAIN) | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 45;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 213;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,2,4,-,INSPECT) | 3;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 13;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 64;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (5,2,4,-,WAIT) | 4;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 16;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 65;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (5,2,4,NORMAL,MAINTAIN) | 6;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 19;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 58;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (5,2,4,STRESSED,INSPECT) | 4;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 27;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 103;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (5,2,4,STRESSED,WAIT) | 2;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 14;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 81;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (5,5,6,-,MAINTAIN) | 30;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | 126;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | 510;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | NORMAL (1, 1, 1):H 1.00 [1..1]; STRESSED (1, 1, 1):H -5.00 [-5..-5] |
| (5,5,9,STRESSED,MAINTAIN) | 10;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 58;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 250;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (5,8,0,-,MAINTAIN) | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 23;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 115;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,8,0,STRESSED,MAINTAIN) | 1;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 30;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 130;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,8,1,-,MAINTAIN) | 6;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 32;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 132;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (5,8,1,NORMAL,MAINTAIN) | na | 22;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 114;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,3,0,-,MAINTAIN) | 1;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 11;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 60;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,3,0,NORMAL,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 7;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 33;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,3,0,STRESSED,MAINTAIN) | 6;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 47;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 215;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,3,2,NORMAL,MAINTAIN) | 6;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 19;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 58;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (6,3,6,-,INSPECT) | 9;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 30;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 117;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (6,3,6,-,MAINTAIN) | 2;m=1.00,1.00,-4.00;sd=0.00,0.00,2.00 | 2;m=1.00,1.00,-4.00;sd=0.00,0.00,2.00 | 2;m=1.00,1.00,-4.00;sd=0.00,0.00,2.00 | NORMAL (3, 1, 0):H -2.00 [-2..-2]; STRESSED (3, 1, 0):H -6.00 [-6..-6] |
| (6,3,6,-,WAIT) | 8;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 30;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 130;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (6,3,6,NORMAL,MAINTAIN) | 5;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 31;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 133;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (6,3,6,STRESSED,INSPECT) | 2;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 18;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 66;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (6,3,6,STRESSED,WAIT) | 2;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 13;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 60;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (6,6,1,-,MAINTAIN) | 13;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,7,-,MAINTAIN) | 13;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,7,STRESSED,INSPECT) | 4;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 29;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 131;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,6,7,STRESSED,WAIT) | 5;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 28;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 118;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,STRESSED,INSPECT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 26;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 115;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,STRESSED,WAIT) | 7;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 34;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 137;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,10,NORMAL,MAINTAIN) | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,9,0,-,MAINTAIN) | 2;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 21;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 113;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,9,0,STRESSED,MAINTAIN) | na | 28;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 128;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,9,2,-,MAINTAIN) | 4;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 30;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 130;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,9,2,NORMAL,MAINTAIN) | na | 20;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 112;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,4,0,NORMAL,MAINTAIN) | 4;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 17;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 56;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,4,0,STRESSED,MAINTAIN) | 2;m=1.00,1.00,0.50;sd=0.00,0.00,0.50 | 2;m=1.00,1.00,0.50;sd=0.00,0.00,0.50 | 2;m=1.00,1.00,0.50;sd=0.00,0.00,0.50 | STRESSED (4, 2, 0):H 0.00 [0..0]; STRESSED (4, 3, 0):H 1.00 [1..1] |
| (7,4,4,NORMAL,MAINTAIN) | 3;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 29;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 131;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,4,8,-,INSPECT) | 12;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 65;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 262;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (7,4,8,-,WAIT) | 19;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 62;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 249;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (7,7,0,-,MAINTAIN) | 11;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 59;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 251;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (7,7,0,STRESSED,MAINTAIN) | 8;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 56;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 248;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,NORMAL,MAINTAIN) | 13;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,STRESSED,INSPECT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 28;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 115;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,STRESSED,WAIT) | 8;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 33;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 138;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,8,-,MAINTAIN) | 11;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 59;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 251;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,9,NORMAL,MAINTAIN) | 10;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 58;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 250;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,10,-,MAINTAIN) | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 26;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 123;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (7,7,10,NORMAL,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 29;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 124;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (8,5,1,NORMAL,MAINTAIN) | 2;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 15;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 54;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,5,5,NORMAL,MAINTAIN) | 1;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 27;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 129;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,0,-,INSPECT) | 11;m=-1.00,-1.00,0.09;sd=0.00,0.00,0.29 | 59;m=-1.00,-1.00,0.02;sd=0.00,0.00,0.13 | 251;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.06 | STRESSED (3, 2, 0):H 1.00 [1..1]; STRESSED (3, 3, 0):H 0.00 [0..0] |
| (8,8,1,STRESSED,MAINTAIN) | 7;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 55;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 247;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,NORMAL,INSPECT) | 10;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 31;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 128;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,NORMAL,WAIT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 32;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 127;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,STRESSED,INSPECT) | 9;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 29;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 122;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,STRESSED,WAIT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 33;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 132;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,8,NORMAL,INSPECT) | 8;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 26;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 120;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,8,NORMAL,WAIT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 35;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 133;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,9,-,INSPECT) | 6;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 32;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 127;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,9,-,WAIT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 27;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 124;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,10,-,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 24;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 121;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (8,8,10,NORMAL,MAINTAIN) | 11;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 85;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 372;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,6,6,NORMAL,MAINTAIN) | na | 25;m=0.00,1.00,1.00;sd=0.00,0.00,0.00 | 127;m=0.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (9,7,7,NORMAL,MAINTAIN) | na | 23;m=0.00,1.00,1.00;sd=0.00,0.00,0.00 | 125;m=0.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (9,9,5,-,INSPECT) | 31;m=-1.00,-1.00,0.97;sd=0.00,0.00,0.18 | 127;m=-1.00,-1.00,0.99;sd=0.00,0.00,0.09 | 511;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.04 | NORMAL (1, 0, 0):H 0.00 [0..0]; NORMAL (1, 0, 1):H 1.00 [1..1]; STRESSED (1, 0, 1):H 1.00 [1..1] |
| (9,9,5,-,MAINTAIN) | 2;m=0.00,0.00,-1.00;sd=0.00,0.00,1.00 | 2;m=0.00,0.00,-1.00;sd=0.00,0.00,1.00 | 2;m=0.00,0.00,-1.00;sd=0.00,0.00,1.00 | NORMAL (1, 0, 1):H 0.00 [0..0]; STRESSED (1, 0, 1):H -2.00 [-2..-2] |

### Per-key repeated outcome table — seed 2503

| key (q,o,h,inspection,action) | r16 n;mean Q/O/H;SD Q/O/H | r64 n;mean Q/O/H;SD Q/O/H | r256 n;mean Q/O/H;SD Q/O/H | r256 variable-health strata |
|---|---|---|---|---|
| (1,4,0,NORMAL,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (1,4,0,STRESSED,MAINTAIN) | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 3;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (2,5,0,NORMAL,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (2,5,0,STRESSED,MAINTAIN) | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (2,5,1,-,MAINTAIN) | 9;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 55;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 244;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (2,5,1,NORMAL,MAINTAIN) | 10;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 37;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 127;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (2,5,1,STRESSED,MAINTAIN) | 6;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 29;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 134;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (3,6,0,-,MAINTAIN) | 8;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 54;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 243;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (3,6,0,NORMAL,MAINTAIN) | 11;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 38;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 128;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (3,6,0,STRESSED,MAINTAIN) | 9;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 32;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 137;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (3,6,3,-,INSPECT) | 20;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 70;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 265;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (3,6,3,-,WAIT) | 11;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 57;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | 246;m=-1.00,-1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (4,4,0,STRESSED,MAINTAIN) | 2;m=1.00,1.00,0.50;sd=0.00,0.00,0.50 | 2;m=1.00,1.00,0.50;sd=0.00,0.00,0.50 | 2;m=1.00,1.00,0.50;sd=0.00,0.00,0.50 | STRESSED (3, 3, 0):H 0.50 [0..1] |
| (4,7,0,-,MAINTAIN) | 9;m=1.00,1.00,0.56;sd=0.00,0.00,0.50 | 55;m=1.00,1.00,0.47;sd=0.00,0.00,0.50 | 244;m=1.00,1.00,0.52;sd=0.00,0.00,0.50 | NORMAL (2, 1, 0):H 1.00 [1..1]; NORMAL (3, 1, 0):H 1.00 [1..1]; STRESSED (2, 1, 0):H 0.01 [0..1] |
| (4,7,0,NORMAL,MAINTAIN) | 12;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 39;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 129;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (4,7,0,STRESSED,MAINTAIN) | 10;m=1.00,1.00,0.30;sd=0.00,0.00,0.46 | 33;m=1.00,1.00,0.09;sd=0.00,0.00,0.29 | 138;m=1.00,1.00,0.02;sd=0.00,0.00,0.15 | STRESSED (2, 0, 0):H 0.00 [0..0]; STRESSED (2, 1, 0):H 0.02 [0..1] |
| (5,5,6,-,MAINTAIN) | 30;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | 126;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | 510;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | NORMAL (1, 1, 1):H 1.00 [1..1]; STRESSED (1, 1, 1):H -5.00 [-5..-5] |
| (5,5,8,-,MAINTAIN) | 1;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 39;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 231;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (5,5,8,NORMAL,MAINTAIN) | na | 9;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 9;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (5,5,9,-,MAINTAIN) | 1;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 15;m=1.00,1.00,-0.80;sd=0.00,0.00,1.47 | 58;m=1.00,1.00,-0.45;sd=0.00,0.00,1.50 | NORMAL (0, 0, 1):H 1.00 [1..1]; STRESSED (0, 0, 1):H -2.00 [-2..-2] |
| (5,5,9,NORMAL,MAINTAIN) | 1;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 8;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 33;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (5,5,9,STRESSED,MAINTAIN) | 7;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 47;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | 220;m=1.00,1.00,-2.00;sd=0.00,0.00,0.00 | constant |
| (5,8,0,-,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 27;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 114;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,8,0,STRESSED,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 25;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 130;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (5,8,1,-,MAINTAIN) | 1;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 22;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 124;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (5,8,1,NORMAL,MAINTAIN) | 5;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 32;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 122;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,1,-,MAINTAIN) | 14;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 62;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 254;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,7,-,INSPECT) | 2;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 13;m=-1.00,-1.00,0.85;sd=0.00,0.00,0.36 | 13;m=-1.00,-1.00,0.85;sd=0.00,0.00,0.36 | NORMAL (0, 0, 0):H 1.00 [1..1]; STRESSED (1, 1, 0):H 0.00 [0..0] |
| (6,6,7,-,MAINTAIN) | 12;m=1.00,1.00,0.42;sd=0.00,0.00,1.93 | 12;m=1.00,1.00,0.42;sd=0.00,0.00,1.93 | 12;m=1.00,1.00,0.42;sd=0.00,0.00,1.93 | NORMAL (0, 0, 0):H 1.00 [1..1]; STRESSED (1, 1, 0):H -6.00 [-6..-6] |
| (6,6,7,-,WAIT) | 2;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 47;m=-1.00,-1.00,0.87;sd=0.00,0.00,0.33 | 258;m=-1.00,-1.00,0.90;sd=0.00,0.00,0.30 | NORMAL (0, 0, 0):H 1.00 [1..1]; STRESSED (1, 1, 0):H 0.00 [0..0] |
| (6,6,7,STRESSED,INSPECT) | 2;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 18;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 117;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,6,7,STRESSED,WAIT) | 4;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 28;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | 102;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,-,INSPECT) | 4;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 16;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 70;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,-,WAIT) | 2;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 17;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 60;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,NORMAL,MAINTAIN) | 5;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 17;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 58;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,STRESSED,INSPECT) | 5;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 23;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 87;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,8,STRESSED,WAIT) | 2;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 19;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 99;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,9,-,MAINTAIN) | na | 38;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 230;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,9,NORMAL,MAINTAIN) | na | 8;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 8;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,6,10,-,MAINTAIN) | na | 5;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 29;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,6,10,NORMAL,MAINTAIN) | 4;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 12;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 37;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,9,0,-,MAINTAIN) | 1;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 25;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 112;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,9,0,STRESSED,MAINTAIN) | 1;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 23;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | 128;m=1.00,0.00,0.00;sd=0.00,0.00,0.00 | constant |
| (6,9,2,-,MAINTAIN) | na | 20;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 122;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | constant |
| (6,9,2,NORMAL,MAINTAIN) | 3;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 30;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | 120;m=1.00,0.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,4,8,-,MAINTAIN) | 30;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | 126;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | 510;m=1.00,1.00,-2.00;sd=0.00,0.00,3.00 | NORMAL (2, 1, 0):H 1.00 [1..1]; STRESSED (2, 1, 0):H -5.00 [-5..-5] |
| (7,7,0,-,MAINTAIN) | 12;m=1.00,1.00,0.08;sd=0.00,0.00,0.28 | 60;m=1.00,1.00,0.02;sd=0.00,0.00,0.13 | 252;m=1.00,1.00,0.00;sd=0.00,0.00,0.06 | STRESSED (3, 3, 0):H 0.00 [0..1] |
| (7,7,0,STRESSED,MAINTAIN) | 7;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 55;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 247;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,-,INSPECT) | 7;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 26;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 115;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,-,WAIT) | 7;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 34;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 131;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,NORMAL,MAINTAIN) | 7;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 29;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 131;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,STRESSED,INSPECT) | 3;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 26;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 78;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,7,STRESSED,WAIT) | 2;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 7;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 51;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,8,-,MAINTAIN) | 9;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 9;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 9;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,9,NORMAL,MAINTAIN) | 8;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 42;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 185;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (7,7,10,-,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 40;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 232;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (7,7,10,NORMAL,MAINTAIN) | 2;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 10;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 10;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (8,5,3,-,MAINTAIN) | 13;m=1.00,1.00,-3.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,-3.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,-3.00;sd=0.00,0.00,0.00 | constant |
| (8,5,9,-,MAINTAIN) | 13;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 61;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | 253;m=1.00,1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,0,-,INSPECT) | 11;m=-1.00,-1.00,0.09;sd=0.00,0.00,0.29 | 59;m=-1.00,-1.00,0.02;sd=0.00,0.00,0.13 | 251;m=-1.00,-1.00,0.00;sd=0.00,0.00,0.06 | STRESSED (2, 2, 0):H 0.00 [0..0]; STRESSED (3, 2, 0):H 1.00 [1..1]; STRESSED (3, 3, 0):H 0.00 [0..0] |
| (8,8,1,STRESSED,MAINTAIN) | 5;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 53;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | 245;m=1.00,1.00,-1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,-,INSPECT) | 14;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 64;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 262;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,6,-,WAIT) | 15;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 61;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 247;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,8,NORMAL,INSPECT) | 3;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 13;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 68;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,8,NORMAL,WAIT) | 3;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 15;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 62;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,9,-,INSPECT) | 4;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 4;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 4;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,9,-,WAIT) | 4;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 4;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | 4;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.00 | constant |
| (8,8,10,-,MAINTAIN) | 1;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 38;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 230;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (8,8,10,NORMAL,MAINTAIN) | 8;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 50;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | 193;m=1.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,6,0,-,MAINTAIN) | 12;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 60;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 252;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,6,10,-,MAINTAIN) | 12;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 60;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 252;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,7,0,-,MAINTAIN) | 10;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 58;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 250;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,7,10,-,MAINTAIN) | 11;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 59;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 251;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,8,0,-,MAINTAIN) | 8;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 56;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 248;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,8,10,-,MAINTAIN) | 8;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 56;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | 248;m=0.00,1.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,9,0,-,MAINTAIN) | 9;m=0.00,0.00,0.00;sd=0.00,0.00,0.00 | 57;m=0.00,0.00,0.00;sd=0.00,0.00,0.00 | 249;m=0.00,0.00,0.00;sd=0.00,0.00,0.00 | constant |
| (9,9,5,-,MAINTAIN) | 2;m=0.00,0.00,-1.00;sd=0.00,0.00,1.00 | 2;m=0.00,0.00,-1.00;sd=0.00,0.00,1.00 | 2;m=0.00,0.00,-1.00;sd=0.00,0.00,1.00 | NORMAL (1, 0, 1):H 0.00 [0..0]; STRESSED (1, 0, 1):H -2.00 [-2..-2] |
| (9,9,5,-,WAIT) | 31;m=-1.00,-1.00,0.97;sd=0.00,0.00,0.18 | 127;m=-1.00,-1.00,0.99;sd=0.00,0.00,0.09 | 511;m=-1.00,-1.00,1.00;sd=0.00,0.00,0.04 | NORMAL (1, 0, 0):H 0.00 [0..0]; NORMAL (1, 0, 1):H 1.00 [1..1]; STRESSED (1, 0, 1):H 1.00 [1..1] |
| (9,9,10,-,MAINTAIN) | 6;m=0.00,0.00,-1.00;sd=0.00,0.00,0.00 | 54;m=0.00,0.00,-1.00;sd=0.00,0.00,0.00 | 246;m=0.00,0.00,-1.00;sd=0.00,0.00,0.00 | constant |

Notable high-depth examples: the key (5,5,6,None,MAINTAIN) had 510–511 observations per seed at 256 repetitions and mean Q/O/H of +1/+1/−2, with health SD about 3 and observed health changes −5 to +1, associated with NORMAL versus STRESSED despite the same recorded starting history tuple. The key (7,4,8,None,MAINTAIN) similarly had 510 observations per seed, mean +1/+1/−2 and health SD 3, with −5 to +1 across conditions under the same initial history tuple. These are evidence of outcome variation under an identical agent key, not proof that condition alone caused each difference.

### 7. INSPECT and reveal contexts

INSPECT count equals the number of reveals made available to the next decision in each run. It ranged from 163–213 at 16 repetitions, 427–689 at 64, and 1,425–2,573 at 256. All runs used all three decision-time inspection-result contexts: None, NORMAL, and STRESSED. Unique state counts by context ranged across runs from 49–75 None, 42–53 NORMAL, and 41–48 STRESSED.

Between 46 and 55 physical (quality, oxygen, health) triples per run appeared in more than one inspection-result partition. This is direct evidence that including the revealed result creates separate exact keys for some otherwise identical physical observations. Whether that separation improves later choices was not evaluated and must not be inferred from reveal counts.

### 8. Reproducibility, limits, and interpretation

All nine summaries recorded the prescribed seed, repetition count through episode totals, and the same eight-entry manifest. Each run began by calling the existing runner with fresh memory and a newly seeded RNG. No identical repetition/seed configuration was run twice because no duplicate artifact existed; I relied on Task 23’s reproducibility test source and did not execute tests under this task. The nested coverage prefixes are reported as checkpoints, not as an independent identical-run reproducibility test.

**C is the dominant result, with B secondary.** Increasing recurrence materially improves coverage from 16 to 64 repetitions, but coverage largely saturates from 64 to 256. Exact-key support remains sparse in a different sense: roughly half of visited states have only one or two action estimates, many downstream states are singletons, and sample depth is highly concentrated. More volume alone does not remove observed health-outcome variation inside identical keys.

Recommend a separate representation/generalization design study that preserves the current agent observation boundary and addresses partial observability explicitly. Any model change, training-policy change, or evaluation requires its own authorization. Do not run a held-out comparison until that design/coverage question is reviewed.

No held-out evaluation was run. No Box source, tests, configuration, baselines, environment dynamics, observation permissions, or historical artifacts were modified. No tests were run. The only local writes were the nine authorized Task 25 output directories; this completion report is the only bridge write, and no raw artifact or source code was uploaded.
