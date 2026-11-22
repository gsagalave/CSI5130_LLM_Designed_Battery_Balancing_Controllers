---
name: battery-balancing-baselines
description: Implement and compare rule-based, NNLS, random, Optuna, and optional reinforcement-learning balancing baselines.
---

# Battery Balancing Baselines

Use this skill for controller implementations and baseline comparisons under `baselines/`.

## Instructions

- Implement the rule-based controller from the earlier project and document its threshold behavior.
- Implement or reproduce the NNLS optimal balancing controller as described in the README.
- Implement random search and Optuna using the same scenario set and simulation budget as LLM methods.
- Add PPO only if dependencies and a bounded experiment plan are available.
- Give every method equivalent controller budgets, seeds, scenario sampling, and stopping rules.
- Use the same safety wrapper and metric pipeline for all methods.

## Workflow

1. Load the shared controller interface and scenario configuration.
2. Implement or port one baseline at a time.
3. Register the baseline with the shared experiment runner.
4. Run design scenarios and collect attempt counts and metrics.
5. Run held-out scenarios without tuning on their results.
6. Export records in a schema compatible with the evaluation skill.

## Inputs

- Twin and scenario API
- Baseline-specific parameters or search spaces
- Random seed and attempt budget
- Safety limits
- Design and held-out scenario sets

## Outputs

- Baseline controller implementations
- Parameter or learned-policy artifacts
- Per-scenario and aggregate metrics
- Attempt and final-performance logs

## Verification Steps

- Run a 4-cell smoke scenario for every baseline.
- Compare identical random seeds and budgets across methods.
- Verify that a rule-based controller's threshold and hysteresis behavior are traceable.
- Confirm that optimization does not tune on held-out scenarios.
- Check that no baseline bypasses shared safety validation.
