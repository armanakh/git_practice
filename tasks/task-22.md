# Task 22: Structured Agent Training
## Stage 1: Read-only implementation and feasibility inspection

**Status:** Issued for read-only inspection and design proposal. Implementation is NOT authorized by this task.

## 1. Purpose

Design a structured training phase for the existing FiT agent. The immediate research objective remains to improve the agent's independent performance against the existing fixed-threshold baseline, while preserving the broader purpose of The Box and FiT.

The training phase should help the agent connect:
- its own action choices,
- predictions made before action execution,
- the actual changes produced by the environment,
- an explanation of why the observed outcome represents a gain, loss, or mixed/no immediate change,
- and updates to the agent's learned experience.

This is a training-process change, not an environment redesign.

## 2. Decisions already agreed

### 2.1 Agent-directed action selection

During training, the agent chooses its own actions. The training system must not prescribe actions, override the agent, or force equal action frequencies.

If the agent repeatedly chooses WAIT despite meaningful opportunities to choose INSPECT or MAINTAIN, that is an agent result to measure, not something the training system should silently correct.

### 2.2 Fair opportunity across all three actions

The experimenter is responsible for ensuring that the training scenario design gives WAIT, INSPECT, and MAINTAIN meaningful opportunities to be useful. The opportunity set should include contexts in which each action can lead to beneficial outcomes, and contexts in which actions can lead to poor or mixed outcomes, where supported by the established FiT mechanics.

Equal opportunity does NOT mean:
- equal action counts,
- forced exploration,
- forced action rotation,
- equal rewards,
- or equal success rates.

Do not change the agent's action selection to manufacture balance. Measure the actual action distribution and the opportunities presented.

Before proposing scenario changes, inspect the current action semantics and FiT dynamics. Do not assume that all actions have immediate physical effects. In particular, INSPECT may have informational value rather than immediate environmental benefit.

### 2.3 Prediction timing

The agent must record its prediction before the selected action is executed. The prediction must not have access to the resulting transition or evaluator feedback.

The inspection should identify the existing prediction representation and determine the smallest compatible way to record the pre-action prediction. Do not invent a new prediction target without explaining why it is necessary.

### 2.4 Evaluator feedback

After the action executes, an evaluator provides feedback that:
- describes the actual state changes,
- identifies gains, losses, or mixed/no immediate changes,
- explains why the outcome is considered beneficial or harmful,
- distinguishes environmental changes from informational consequences where relevant.

The evaluator assesses outcomes; it does not prescribe actions. It must not say which action the agent should have selected or encode a rule such as “maintain when oxygen is below X”.

### 2.5 Existing FiT mechanics remain unchanged

Do not alter:
- health-loss or health-gain mechanics,
- tank quality or oxygen dynamics,
- existing delayed consequences or history-dependent effects,
- action semantics,
- scenario dynamics,
- agent observation permissions,
- existing baselines,
- Box architecture or authoritative state ownership.

Use the established FiT mechanics as the source of actual consequences. The evaluator interprets those consequences; it must not introduce a replacement health formula or silently invent weights that combine health, quality, and oxygen into a scalar score.

The agreed objective hierarchy is:
1. Fish health: primary objective.
2. Tank quality: supporting environmental measure.
3. Oxygen: supporting environmental measure.

This hierarchy guides transparent explanation. The exact evaluation representation and treatment of trade-offs remain to be inspected and proposed, not assumed.

### 2.6 Training and evaluation separation

Training feedback is available during training. Before held-out evaluation:
- freeze the resulting learned memory/parameters,
- remove evaluator feedback,
- prohibit further learning,
- use unseen evaluation scenarios,
- preserve existing evaluation controls and baselines.

The agent must make independent decisions during evaluation.

## 3. Training interaction to investigate

The intended conceptual loop is:

1. Agent receives its permitted observation.
2. Agent selects an action independently.
3. Agent records a prediction of the expected consequences BEFORE execution.
4. The Box executes the action and updates authoritative state.
5. Evaluator inspects the actual transition and produces a transparent explanation.
6. Agent receives the feedback and updates its learning/experience.
7. Repeat during the training phase.

The prediction and evaluator feedback must be temporally separated to prevent outcome leakage into the prediction.

The existing prediction/experience mechanism should be reused where technically appropriate. Do not replace it merely to make the task easier.

## 4. Training opportunity design

Inspect how training scenarios can provide meaningful opportunities for all three actions without controlling the agent's choices.

The design should consider:
- scenario diversity and coverage,
- contexts where WAIT can be beneficial or harmful,
- contexts where INSPECT can be informationally useful,
- contexts where MAINTAIN can be beneficial or unnecessary/costly,
- whether existing FiT dynamics actually support these distinctions,
- whether delayed and history-dependent consequences are represented in feedback.

Do not fabricate counterfactual outcomes. The evaluator should explain the actual observed transition. If comparisons between actions are proposed, they must be clearly separated from observed facts and must not be presented as actual experience unless the environment really executed those actions.

If current FiT dynamics do not provide meaningful success opportunities for a particular action, report that limitation rather than modifying dynamics under this task.

## 5. Measurements to propose

At minimum, propose how to record and report:

- number of training episodes and decisions,
- scenario/configuration coverage,
- action availability/opportunity by action,
- actual action counts and proportions,
- predictions made before execution,
- prediction accuracy against observed transitions,
- evaluator feedback categories and explanations,
- agent learning updates,
- frozen memory/experience coverage by state and action,
- independent held-out outcomes, including cumulative fish health,
- comparison with the unchanged experience-disabled and fixed-threshold baselines.

Keep prediction accuracy, evaluator assessment, action distribution, and task performance as separate measurements. Do not treat a good evaluator score as proof of good prediction or good decision-making.

## 6. Read-only inspection work requested

Inspect the actual local The Box repository and report:

1. The current training pipeline, agent lifecycle, memory update mechanism, and freeze/evaluation boundary.
2. How the agent currently chooses WAIT, INSPECT, and MAINTAIN.
3. The exact existing action semantics and whether all three can produce beneficial outcomes in different contexts.
4. What the current prediction mechanism predicts, when it predicts, and how prediction records are represented.
5. Where and how actual state transitions can be observed safely by an evaluator without violating Box state ownership or agent observation boundaries.
6. Whether existing FiT mechanics already provide an objective basis for explaining fish-health gains/losses and environmental changes.
7. How INSPECT's informational value can be represented without falsely calling the absence of immediate physical change a loss.
8. The smallest feasible architecture for post-transition evaluator feedback and agent learning from that feedback.
9. How to provide broad, meaningful training opportunity across all three actions without forced action selection or equal-frequency enforcement.
10. Potential leakage, confounds, or ways the evaluator could accidentally encode a preferred action.
11. A proposed minimal implementation plan, tests, artifacts, and evaluation protocol for a subsequent implementation task.

Cite concrete repository paths, classes/functions, and existing tests in the report. Distinguish verified facts from proposals and unresolved questions.

## 7. Strict scope and authorization

This is a READ-ONLY task.

- Do not edit source files.
- Do not add or modify tests.
- Do not change experiment configurations or results.
- Do not run new experiments that mutate or overwrite existing artifacts.
- Do not modify Box architecture, FiT dynamics, agent learning, selector, fallback, reward/evaluation logic, or baselines.
- Do not implement the training phase or evaluator.
- Do not begin Task 22 implementation until the user explicitly reviews and authorizes a separate implementation task.

You may run non-mutating inspection commands and existing read-only tests if safe. Report any command that could modify files or artifacts before running it.

## 8. Completion report format

Return:
1. Executive summary and feasibility verdict.
2. Verified current implementation facts with repository paths and symbols.
3. Action-opportunity analysis for WAIT, INSPECT, and MAINTAIN.
4. Proposed training/evaluator architecture, clearly labelled as a proposal.
5. Proposed scenario and measurement design.
6. Risks, unresolved decisions, and safeguards.
7. Minimal staged implementation plan.
8. Confirmation that no files, tests, configurations, or experiment artifacts were changed.

**Do not implement. Wait for user approval after delivering the inspection report.**


---

## 9. Design resolution after read-only inspection

**Status:** Design decisions resolved. The inspection findings are accepted as the implementation basis. No local The Box source changes are authorized by this addendum. A separate implementation task must be issued before Codex changes source or runs mutating experiments.

The following decisions close the unresolved design questions.

### 9.1 Separate genuine choice from cold-start fallback

The existing WAIT fallback is not evidence that the agent prefers WAIT. When candidate estimates are missing, the selector cannot compare the actions and mechanically returns WAIT. We must not report this as a learned decision.

During training, use an explicit **uncertainty-directed, agent-owned action policy**:

- If one or more available actions have no usable estimate for the current observable state, select one of those unestimated actions using a seeded pseudorandom choice.
- Do not impose quotas, rotations, target frequencies, or experimenter-selected actions.
- Once all available actions have usable estimates, select according to the learned outcome model described below.
- If estimated outcomes are tied, use a seeded random tie-break rather than silently preferring WAIT.
- Record whether each action resulted from uncertainty-directed selection, model-based selection, or tie-breaking.

This is a policy of the agent, not an experimenter intervention. It makes uncertainty actionable without guaranteeing equal counts. The agent may still repeatedly choose the same action by chance. That remains measurable. The seed makes runs reproducible.

This training policy is distinct from the fixed-threshold baseline. It must not be described as evidence of learned preference when it is acting under uncertainty.

### 9.2 Learn and compare a transparent outcome vector, not a scalar reward

For each observable-state/action pair, retain estimates of the actual transition components already supported by the FiT learner:

- expected fish-health change,
- expected tank-quality change,
- expected oxygen change.

The evaluator produces a structured assessment derived from those actual changes:
- health: gain, no change, or loss;
- quality: improved, unchanged, or degraded;
- oxygen: improved, unchanged, or degraded;
- overall description: health-positive, health-negative, or mixed, with the supporting environmental changes stated explicitly.

No weighted sum, invented reward, or hidden score is introduced.

For model-based action comparison, use a **lexicographic ordering** consistent with the agreed hierarchy: compare predicted health change first; only when health predictions tie, compare quality; only when those tie, compare oxygen. This makes the priority explicit and avoids arbitrary exchange rates between unlike quantities. Keep the component predictions visible in records.

The implementation must verify that this is compatible with existing estimate semantics and bounded/clipped transitions. If the current estimates cannot support this comparison without ambiguity, stop and report the exact blocker rather than quietly changing the objective.

### 9.3 Feedback is both explanation and structured learning evidence

Feedback must not be a decorative text annotation. After receiving it, the agent updates its transition experience from the executed action and actual pre/post observations, using the structured outcome components above.

The evaluator's categorical descriptions are derived labels for those observed components, not independent reward values. Store the assessment alongside the transition record so that the agent's learning can be audited. The numerical outcome estimates remain grounded in observed transitions.

The strict order is:
1. capture permitted pre-action observation;
2. choose action;
3. record prediction;
4. execute action;
5. read committed transition;
6. generate evaluator assessment;
7. update experience.

No evaluator output may enter the prediction made earlier in that same decision.

### 9.4 INSPECT is an information action

Do not classify INSPECT as harmful merely because it has no immediate physical benefit. Its feedback must separate:
- immediate physical effects, if any;
- information revealed and when that information becomes available;
- the fact that future decisions can condition on the revealed result.

Do not assign an arbitrary immediate information reward. Its value is assessed through subsequent decisions and episode-level outcomes. The evaluator must not reveal hidden information before INSPECT has legitimately exposed it to the agent. Experimenter-side records may retain hidden ground truth separately for analysis.

### 9.5 Delayed effects and causal attribution

Feedback describes the committed transition and any delayed consequence that actually materialized in that transition. It must distinguish direct action effects from ordinary environmental dynamics and history-dependent penalties wherever the existing records support that distinction.

Do not charge a later penalty to an earlier action as though it occurred immediately. Do not claim that an action caused a downstream outcome unless the recorded mechanics support that attribution. No counterfactual action outcome may be fabricated.

### 9.6 Training and evaluation policy

Training uses uncertainty-directed selection while the model lacks action estimates. The policy then uses the learned outcome vector as estimates become available.

Before held-out evaluation:
- freeze all learned estimates;
- disable evaluator feedback and all learning updates;
- disable uncertainty-directed exploration;
- use the frozen model-based selector only;
- retain the existing experience-disabled and fixed-threshold baselines unchanged.

If the frozen model lacks estimates for a state/action comparison at evaluation time, record the missing coverage and use a clearly identified deterministic fallback. Do not disguise fallback decisions as model-based choices.

Use the same held-out scenario manifest for all policies. Report training action-selection modes separately from evaluation action-selection modes.

### 9.7 INSPECT value and evaluation

Report INSPECT at two levels:
- immediate transition: physical changes and information revealed;
- episode level: subsequent actions and cumulative fish health, alongside the other environmental measures.

Do not infer that INSPECT was beneficial solely because information was revealed, nor that it was harmful because it lacked immediate physical benefit. Its decision value must be demonstrated by later behaviour and outcomes, compared against the unchanged baselines on matched scenarios.

### 9.8 Implementation boundary and required tests

The implementation belongs in the FiT experiment/agent layer. Do not alter Box core or FiT world dynamics.

A separate implementation task must include tests proving:
- pre-action predictions are captured before execution and cannot see evaluator output;
- evaluator reads committed transitions and cannot mutate authoritative state;
- agent-facing feedback contains no unrevealed hidden condition/history/penalty data;
- actual executed transitions alone update experience;
- the outcome vector and lexicographic comparison follow the stated hierarchy without scalarization;
- uncertainty-directed selection only acts when estimates are insufficient, uses a reproducible seed, and imposes no quotas or action-frequency targets;
- tie-breaking is reproducible;
- INSPECT feedback separates physical and informational effects;
- delayed consequences are described at the cycle in which they materialize;
- evaluation has frozen memory, no feedback, no learning, and no exploration;
- missing evaluation coverage is explicitly recorded;
- existing baselines and FiT mechanics remain unchanged.

First implement and test the training/evaluator mechanics. Then run a training validation to inspect whether the scenario manifest genuinely presents useful opportunities. Only after that should a separate evaluation run be authorized. Preserve all raw artifacts and report prediction accuracy, evaluator assessments, action distribution, memory coverage, and task outcomes as separate dimensions.

**These decisions resolve the design questions identified in the Task 22 inspection report. They do not authorize implementation.** The next step is to issue a separate implementation task for explicit approval.
