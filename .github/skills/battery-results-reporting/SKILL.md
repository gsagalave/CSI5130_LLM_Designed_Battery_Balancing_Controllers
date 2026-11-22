---
name: battery-results-reporting
description: Organize battery experiment evidence and prepare accurate tables, plots, report sections, and presentation artifacts.
---

# Battery Results Reporting

Use this skill for evidence summaries and report or presentation work under `report/`.

## Instructions

- Keep claims tied to scenario, seed, run count, and measured metrics.
- Clearly label exploratory, design, and held-out test results.
- Include trade-off plots, Pareto summaries, and performance-versus-budget curves.
- Distinguish numerical observations from interpretations and hypotheses.
- Reproduce all final figures from saved result artifacts when possible.
- Report the LLM, prompt mode, feedback rounds, cost, runtime, and attempt budget used.

## Workflow

1. Load the evaluation manifest and validated result records.
2. Select the claims and figures requested.
3. Generate tables or plots from machine-readable artifacts.
4. Summarize methods, conditions, uncertainty, and limitations.
5. Cross-check every claim against source values.
6. Export an organized report or presentation-ready artifact.

## Inputs

- Validated experiment records
- Evaluation summary
- Simulink validation results
- Copilot evaluation results
- Report or presentation scope

## Outputs

- Results tables
- Trade-off and comparison plots
- Method and limitation summaries
- Report-ready text grounded in evidence
- Reproduction commands or manifests

## Verification Steps

- Recompute at least one reported value from raw records.
- Confirm figure labels and units.
- Ensure no held-out result is described as a design result.
- Check that every caveat is traceable to a real limitation.
- Verify all generated artifacts are saved under `report/` or an explicit artifact directory.
