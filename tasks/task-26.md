# Task 26: Partial Observability and Agent-Visible Representation Design Study

**Status:** READ-ONLY DESIGN STUDY AUTHORIZED  
**Depends on:** Tasks 23, 24, and 25  
**Repository:** Local The Box repository. GitHub bridge is coordination/reporting only.

## 1. Objective

Determine why the Task 23 agent's exact observable-state/action model remains unreliable after substantial recurrence, and identify the smallest defensible next learning-design step.

Task 25 completed 8,064 training episodes and 48,384 decisions across three seeds and three recurrence levels. Coverage improved substantially from 16 to 64 repetitions, then largely plateaued by 64–128. At 256 repetitions, many state/action keys had hundreds of observations, yet 7–13 repeated keys per run still showed different Fish-health outcomes under the same agent-visible key. Some differences aligned with experimenter-only condition/history strata; some remained within the same recorded condition/history-counter stratum. Quality and oxygen changes did not show within-key variability in the reported analysis.

The working diagnosis is that more recurrence alone cannot resolve all relevant variation. The agent's current representation may omit useful information available in its own interaction history, or the environment may remain partially observable even after that history is considered.

This task must distinguish those possibilities. It is a design and evidence study, not an implementation task or policy-performance evaluation.

## 2. Fixed boundaries

Use the existing Task 23 implementation and Task 25 outputs as the evidence base. Inspect local source and artifacts read-only.

Do not:
- modify source, tests, configuration, experiment runners, or artifacts;
- run new training, validation, or held-out experiments;
- change Box core, FiT dynamics, agent observation permissions, evaluator, learner, selector, baselines, or historical results;
- pass hidden condition, pending penalties, history counters, recovery progress, scenario identity, or any other experimenter-only variable to the agent;
- fabricate counterfactual transitions or infer unobserved action outcomes;
- claim that lower in-sample collision or prediction error proves better policy performance.

Preserve the distinction between:
1. information directly available to the agent at decision time;
2. information the agent could legitimately derive from its own past observations, actions, and feedback;
3. experimenter-only diagnostic information available in traces or environment internals.

A candidate representation may use category 1 and causally available category 2 only. Category 3 may be used to diagnose possible causes, never as an agent feature.

If the required Task 25 artifacts are unavailable or incomplete, report the limitation rather than reconstructing or inventing results.

## 3. Required investigation

### 3.1 Verify the actual information boundary

Inspect the Task 23 agent interface, structured training loop, evaluator feedback, memory update path, and Task 25 trace schema.

Document exactly what the agent knows at each decision:
- current observation fields;
- current inspection result and when it becomes available;
- previous observations and actions, if retained;
- previous evaluator feedback and transition deltas, if retained;
- whether any episode/cycle information is legitimately visible;
- what is discarded before the next decision.

Do not assume that a field is agent-visible merely because it appears in a trace or environment object. Trace each field to the actual agent-facing call boundary.

### 3.2 Diagnose the within-key outcome variation

Use Task 25's repeated state/action keys and traces to identify the keys with variable Fish-health outcomes.

For each such key, inspect the preceding agent-visible trajectory and, separately, experimenter-only context. Determine whether the differing outcomes are:
- distinguishable from the agent's available history before action selection;
- correlated with a visible inspection result or prior action/feedback sequence;
- associated only with hidden condition/history/pending-effect variables;
- or unresolved by the available evidence.

Explicitly check whether two records with the same current state/action key and the same recorded experimenter condition/history-counter tuple can still differ because of pending delayed penalties, action-history details, or another state variable omitted from the summary.

Do not infer causation from correlation. State which distinctions are directly verified and which remain hypotheses.

### 3.3 Compare candidate agent-legal representations

Assess, at minimum, these alternatives:

A. **Current exact key:** (quality, oxygen, health, inspection result, action), with no additional history.

B. **Finite observable history:** augment the decision representation with a small, causally available window of previous observations, chosen actions, and feedback. Consider at least one-step history and a short bounded window. No hidden fields.

C. **Compact agent-maintained belief/context state:** maintain a summary derived only from the agent's own observation/action/feedback stream, intended to represent uncertainty about hidden dynamics or latent context. Explain what update information is available and what assumptions are required.

D. **Uncertainty-aware outcome model:** retain the current observable key but represent outcome variability/distributions or confidence rather than relying only on a point running mean. Explain how the selector could account for uncertainty without introducing arbitrary scalar rewards or changing the health-first, quality-second, oxygen-third objective.

E. **Observable-state generalization:** assess whether nearby observable states can share information safely. Identify threshold, clipping, inspection-result, and hidden-context risks. Do not implement bins or nearest-neighbour logic.

For each alternative, compare:
- whether it can distinguish the observed variable-outcome cases using only legal information;
- expected effect on key sparsity and sample sharing;
- risk of overfitting or state explosion;
- compatibility with the current learning and lexicographic selection design;
- implementation complexity;
- what evidence would falsify its usefulness.

### 3.4 Offline, read-only feature-separation analysis

Where trace fields permit, perform a strictly offline partition diagnostic on already collected Task 25 transitions.

For each repeated variable-outcome key, compare grouping by:
- current exact key alone;
- current key plus one preceding agent-visible transition;
- current key plus a short bounded agent-visible history;
- current key plus experimenter-only context, for diagnosis only.

Report group counts, support/sample sizes, and within-group Fish-health outcome variability. Keep the experimenter-only grouping in a visibly separate diagnostic table.

Do not train or update a new agent. Do not use post-action outcomes, future observations, or hidden fields as features for the decision that preceded them. Do not use a partition's lower in-sample variance as proof of predictive validity. Note selection bias: only actions actually selected in the original runs have observed outcomes.

If the available traces do not preserve the required time-ordered agent-visible history, state exactly what is missing and do not approximate it with hidden data.

## 4. Decision criteria

The study should answer:

1. Is the main defect **state aliasing that could be reduced using the agent's own interaction history**, or **irreducible partial observability under the current interface**, or a mixture?
2. Does the current point-mean model discard consequential uncertainty that should remain explicit?
3. Which candidate offers the smallest defensible next step without expanding the observation permissions?
4. What specific experiment would test that candidate while preserving a clean comparison to the current Task 23 learner and fixed-threshold baseline?

Do not select a more complex architecture merely because it is more sophisticated. Prefer the least complex representation/model that addresses an evidenced failure mode.

## 5. Deliverable

Append a report to this task document and push it to the bridge. Include:

1. Exact source files/functions and Task 25 artifact directories inspected.
2. Verified agent information boundary, with the actual decision-time data path.
3. Variable-outcome keys and verified versus hypothesized causes.
4. Candidate A–E comparison, including limitations and risks.
5. Offline history-partition diagnostics, if supported by trace contents.
6. Clear answer to the four decision questions in Section 4.
7. Recommended next step and a proposed narrowly scoped experiment design.
8. Explicit confirmation that no source, tests, configuration, artifacts, baselines, Box core, FiT dynamics, observation permissions, or historical results were changed, and no new experiment or held-out evaluation was run.

## 6. Authorization

Codex is authorized to perform read-only inspection and analysis of existing local source and Task 25 artifacts, and to append this design report to the bridge task document.

**No implementation, tests, new experiment runs, training, evaluation, or changes to project files or artifacts are authorized.**
