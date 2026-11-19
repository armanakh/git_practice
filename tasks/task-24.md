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
