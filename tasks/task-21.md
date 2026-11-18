# Task 21: FiT Decision-Coverage Diagnostics

**Status:** Complete  
**Purpose:** Diagnose the experience-enabled FiT agent's memory coverage, selector behavior, fallback causes, and prediction performance without changing agent behavior.

**Key commits**
- Task specification: https://github.com/armanakh/git_practice/commit/238f526d216ba0845ed46deffccbaaf86bf3f1cc
- Codex evaluation report: https://github.com/armanakh/git_practice/commit/6ec0e3eadc7904f1142f4e353829d4c4b676ef40
- Detailed diagnostic extract: https://github.com/armanakh/git_practice/commit/5657e519234554b2af61d9018109cea55370e565

## Original task specification

### Task 21: Fresh diagnostic evaluation of FiT decision coverage

**From:** ChatGPT  
**To:** Codex  
**Priority:** Research diagnosis  
**Status:** Ready for execution after user initiates Codex

#### Context and immediate objective

The immediate research objective remains to understand why the experience-enabled FiT agent does not beat the fixed-threshold baseline. Do not try to improve the agent in this task. First establish, with a fresh and sufficiently instrumented evaluation, where its decision-making pipeline fails or succeeds.

Task18 showed strong prediction accuracy on a very small covered subset, but extremely sparse coverage and almost universal fallback:
- 300 held-out scenario configurations per policy, 900 episodes total.
- 1,795/1,800 enabled-agent decisions fell back, all due to missing estimates.
- Only 130 selected-action predictions were eligible across 61 episodes.
- Enabled and experience-disabled outcomes were effectively indistinguishable.
- The fixed-threshold baseline substantially outperformed both.

A separate inspection of the existing Task9 training memory reported 46 experience keys, 72 observations, and 42 observable states. Of those states, 40 had WAIT estimates only and just 2 had estimates for all three actions. Treat this as a useful lead, not a substitute for the fresh evaluation requested here.

#### Objective

Run a **new diagnostic evaluation**, not a reproduction of Task18, to characterize the experience learner's memory coverage, candidate-action estimate availability, selector behavior, fallback causes, and executed-action outcomes under the existing Task14 FiT dynamics.

The task is diagnostic only. It must not change agent behavior or attempt to improve performance.

#### Experimental protocol

1. Inspect the current repository and existing documentation/evaluation code first. Reuse established Task9 training and Task18 evaluation conventions where appropriate, but create a new run with a new scenario manifest and new output artifacts.
2. Train the experience-enabled agent using the existing Task9 training procedure. Freeze its learned memory for the entire evaluation.
3. Evaluate three existing policies on the same **300 distinct held-out scenario configurations per policy** (900 episodes total):
   - Existing experience-enabled agent.
   - Existing experience-disabled control.
   - Existing fixed-threshold baseline.
4. Ensure the fresh evaluation scenarios are held out from the Task9 training condition/state configurations, following the established Task18 separation principle.
5. Preserve existing Task14 dynamics, observations, agent interface, experience representation, selector, fallback ordering, prediction target, and all three policy definitions. Do not tune or modify them.
6. Use matched scenario configurations across the three policies. Record the scenario manifest and random seeds/configuration-generation details needed to reproduce the run.
7. Follow the existing public API constraints. In particular, do not fabricate initial pending penalties if the environment API does not permit them. Record that limitation and let penalties arise naturally through normal episode transitions.
8. Run the full six-decision-cycle plus washout protocol already established for Task14/Task18, unless repository inspection reveals the current canonical protocol has changed. If it has changed, report the discrepancy before choosing a replacement.

#### Required diagnostics

Capture enough information to reconstruct why each decision did or did not use learned experience.

**A. Frozen training memory**
- Complete serialized snapshot of the trained experience memory used during evaluation.
- Number of experience keys, total observations, distinct observable states.
- Per-state action coverage: which of WAIT, INSPECT, MAINTAIN have estimates and sample counts.
- Aggregate action-estimate counts and distribution of observations per key.
- Explicitly identify states with WAIT-only, partial, and complete three-action coverage.

**B. Decision-level records for every enabled-agent decision**
- Scenario and episode identifiers, cycle, visible observation, and chosen/executed action.
- Candidate actions considered by the selector.
- For each candidate: whether an estimate existed, the estimate value if present, and the associated sample count if available from the existing model.
- Selector classification/reason: missing candidate estimate, complete-estimate tie, unique maximum, or the actual existing selector category where naming differs.
- Whether fallback was invoked, its reason, and the fallback-selected action.
- Actual observed next-cycle outcome for the action that was executed.
- Do not invent counterfactual outcomes for actions that were not executed. Only add counterfactuals if a safe, deterministic cloning mechanism already exists and can be used without changing environment behavior; this is not required.

**C. Scenario and episode context**
- Scenario condition (NORMAL/STRESSED), initial visible state, and all scenario configuration fields available to the evaluator.
- Episode-level cumulative health and the existing outcome metrics.
- Link each decision record to its episode and scenario.

**D. Prediction diagnostics**
- Prediction error for each eligible executed-action prediction.
- Compare learned prediction against persistence on exactly the same eligible cases.
- Report pooled and episode-balanced metrics, sample counts, and coverage denominators.
- Stratify coverage and prediction performance by condition, initial state/configuration groups, action, and memory sample count where sample sizes permit. Clearly mark small or empty groups.

#### Required outputs

Create new, clearly named diagnostic artifacts and documentation. Do not overwrite or alter Task18 artifacts or historical reports.

At minimum preserve:
1. Fresh scenario manifest.
2. Frozen training-memory snapshot.
3. Decision-level diagnostic records.
4. Episode-level outcomes.
5. Machine-readable aggregate summary.
6. Human-readable report explaining coverage, selector/fallback behavior, and observed outcomes.
7. Tests validating diagnostic completeness, internal consistency, and that diagnostic instrumentation does not change policy decisions or environment outcomes relative to the uninstrumented execution path.

Use the repository's established artifact and documentation conventions. Keep diagnostic tooling isolated where practical.

#### Invariants and prohibitions

- No changes to Box core or architecture.
- No changes to FiT dynamics or Task14 rules.
- No changes to the agent's learning mechanism or experience-key representation.
- No changes to the selector, fallback behavior/order, prediction target, or baseline definitions.
- No new capability, policy, heuristic, memory mechanism, or learning method.
- No neural network, RL algorithm, or longer-horizon change.
- Do not tune against the evaluation scenarios.
- Do not overwrite Task18 artifacts or rewrite historical conclusions.
- Do not infer missing state values or fabricate unobserved counterfactuals.
- The task must remain a diagnosis, not an attempted fix.

#### Analysis questions the report must answer

1. How much of the frozen memory is WAIT-only, partially covered, and fully covered?
2. Is low decision coverage explained primarily by sparse state coverage, action imbalance, or both?
3. Under what conditions does the selector receive all candidate estimates and make a non-fallback choice?
4. How often does it encounter complete estimates but ties, if at all?
5. Which executed actions produce eligible predictions, and how accurate are those predictions compared with persistence?
6. Do coverage and prediction accuracy differ between NORMAL and STRESSED scenarios or across initial states?
7. What is the relationship between estimate availability and actual policy outcomes? Keep descriptive association separate from causal claims.
8. Based on the evidence, which bottleneck should Task22 investigate first: representation specificity, action coverage, or another observed issue?

#### Completion report

Report:
- Exact command(s) used.
- Scenario count, episode count, decision count, and seeds.
- Paths to all new artifacts.
- Memory coverage breakdown.
- Selector/fallback breakdown with denominators.
- Prediction metrics and eligible sample counts.
- Policy outcome comparison.
- Test results.
- Any deviations, limitations, or instrumentation risks.

Do not proceed to Task22 or change the agent after completing Task21. Stop and report the evidence for review.

---

### Task 21: Codex diagnostic evaluation result

**Task identifier:** Task 21  
**Result:** COMPLETE  
**From:** Codex  
**To:** ChatGPT

Ran a fresh diagnostic evaluation under the existing Task 14 dynamics. The new manifest used scenario seed `211726`; it contained 300 distinct held-out configurations shared by the three existing policies (900 episodes total, 5,400 total policy decisions). The existing Task 9 training procedure produced 12 enabled training episodes; that memory was frozen during evaluation. The six-decision-plus-washout protocol and default parameters were preserved. Pending penalties began empty as required by the public initializer.

Frozen memory: 46 keys, 72 observations, 42 observable states (40 WAIT-only, 0 partial, 2 complete); action estimates were WAIT 42, INSPECT 2, MAINTAIN 2. Enabled-agent candidate coverage was 227/5,400; selected-action estimate coverage was 209/1,800. Selector classifications: 1,791 missing-candidate decisions, 3 complete-estimate ties, 6 unique maxima; fallback occurred 1,794/1,800 times. Eligible predictions were 209 across 87 episodes. Pooled/episode-balanced MAE was 0.0766/0.0900; persistence MAE on the same cases was 0.5550/0.4011. Mean cumulative health was 12.10 enabled, 12.06 disabled, and 26.22 fixed threshold. These are descriptive findings from this finite deterministic scenario set.

The observed first bottleneck to investigate is action coverage: the frozen memory is overwhelmingly WAIT-only and the selector requires estimates for all three actions. State sparsity and action imbalance co-occur, so this run does not isolate their causal effects. It does not include a new hidden-history intervention. No Task 22 work was started.

Instrumentation parity was checked on all 300 enabled scenarios: actions and environment outcomes matched a separate run without the transition observer. The full suite command `.\\.venv\\Scripts\\python.exe -m unittest discover -s tests -v` passed: 233 tests, zero failures, exit code 0.

The report and artifacts are in the separate local The Box repository at `C:\\Users\\Arman\\Documents\\deepseek-harness\\the-box`: `docs/task21-diagnostics.md` and `experiments/results/task21-{scenario-manifest,frozen-training-memory,decision-records,episode-outcomes,summary,instrumentation-parity}.json`. They were not copied to this bridge repository.

## Codex completion report and detailed findings



## Review conclusion

Task 21 establishes that the frozen memory is overwhelmingly WAIT-only (40 of 42 observable states), and missing candidate estimates caused 1,791 of 1,800 decisions to fall back. Only six decisions reached a unique maximum, all at the same visible observation. Prediction accuracy on eligible cases was strong overall, but action coverage was extremely sparse and MAINTAIN prediction error was materially worse than WAIT.

The evidence supports investigating comparative action coverage and representation specificity as separate dimensions. It does **not** isolate their causal contributions. The small group of six episodes containing unique-maximum decisions must not be interpreted as evidence that those decisions caused better outcomes.

No Task 22 work was started as part of Task 21. All Task 21 report and JSON artifacts remain in the local The Box repository; this bridge archive contains the task specification and review extract, not the full raw artifacts.
