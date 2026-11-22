---
name: battery-project-reproducibility
description: Configure and document the battery project's Python environment, dependencies, run commands, artifacts, and reproducible experiments.
---

# Battery Project Reproducibility

Use this skill for repository setup, dependency manifests, command-line entry points, environment configuration, and README run instructions.

## Instructions

- Preserve the README's repository structure:
  - `twin/`
  - `optimizer/`
  - `baselines/`
  - `agent/`
  - `eval/`
  - `report/`
- Prefer NumPy, SciPy, Optuna, OpenRouter API, and Gradio or Streamlit only when selected by the implementation.
- Add dependencies through a package manifest rather than ad hoc installs.
- Pin or constrain versions when reproducibility matters.
- Save scenarios, seeds, configuration, logs, metrics, prompts, and model identifiers.
- Document setup and run commands for simulation, baseline, optimization, evaluation, and copilot workflows.

## Workflow

1. Inspect the existing environment and manifest.
2. Add only dependencies required for the implementation.
3. Create focused CLI entry points or scripts.
4. Add tests or smoke commands for key workflows.
5. Write or update README setup and run instructions.
6. Verify clean setup and one end-to-end smoke workflow.

## Inputs

- Requested feature or experiment
- Existing package and test configuration
- Environment variables
- Experiment configuration

## Outputs

- Dependency or environment updates
- Reproducible run commands
- Configuration and artifact layout
- README setup instructions
- Smoke-test evidence

## Verification Steps

- Create the environment from the documented manifest.
- Run the smallest relevant test or smoke command.
- Confirm required secrets are documented as environment variables.
- Confirm generated files are not added to git accidentally.
- Re-run from a clean temporary directory or environment when practical.
