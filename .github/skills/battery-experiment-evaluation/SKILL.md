---
name: battery-experiment-evaluation
description: Evaluate controller speed, energy efficiency, heat, safety, generalization, and LLM design choices.
---

# Battery Experiment Evaluation

Use this skill for experiment scripts, aggregation, statistical tests, and trade-off plots under `eval/`.

## Instructions

- Evaluate balancing time, energy loss, cell heat, peak and RMS current, completion rate, and safety violations.
- Compare LLM methods with rule-based, NNLS, random-search, Optuna, and optional PPO baselines.
- Separate design scenarios from held-out test scenarios.
- Report performance versus number of simulations or controller attempts.
- Analyze Pareto trade-offs rather than collapsing unlike objectives without disclosure.
- Study prompt or physics context, feedback rounds, tuning mode, code mode, pack size, load, and heterogeneity.
- Preserve experiment configuration, seeds, package versions, model names, prompts, costs, and timestamps.

## Workflow

1. Load scenario and result manifests.
2. Filter and validate result records.
3. Aggregate scenario metrics and uncertainty across seeds.
4. Compute success, safety, and Pareto summaries.
5. Produce scripts or artifacts for plots and tables.
6. Generate a concise interpretation tied to measured evidence.

## Inputs

- Experiment manifest
- Scenario configuration
- Controller result records
- Baseline and LLM method metadata
- Random seeds and repeated-run counts

## Outputs

- Aggregated metric tables
- Trade-off and Pareto plots
- Performance-versus-budget curves
- Held-out generalization results
- Statistical summaries and reproducibility metadata

## Verification Steps

- Confirm design and test scenario identifiers do not overlap.
- Test aggregation with missing and failed runs.
- Verify that success-rate denominators include all planned scenarios.
- Check that reported totals use consistent units.
- Re-run a manifest to confirm deterministic artifacts.
