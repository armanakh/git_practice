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
