---
name: battery-simulator-validation
description: Validate the fast Python battery twin against the detailed Simulink model and select final controllers for confirmation.
---

# Battery Simulator Validation

Use this skill for cross-model validation and final Simulink checks.

## Instructions

- Compare the Python twin with the original Simulink model using identical initial conditions and operating modes.
- Report absolute and relative agreement for balancing time, energy loss, and cell currents.
- Document assumptions, including average-current modeling and omitted switching pulses.
- Preserve the same source scenario and controller definitions across both models.
- Perform a final check of the strongest Pareto and overall candidates in Simulink.
- Do not claim that Python agreement proves exact circuit-level behavior.

## Workflow

1. Define a reference scenario and expected Simulink interface.
2. Export the scenario into a repeatable twin and Simulink format.
3. Run matching tests and normalize units and time bases.
4. Quantify errors and agreement.
5. Select Pareto and overall candidates for final Simulink confirmation.
6. Record model limitations and discrepancies.

## Inputs

- Reference scenarios
- Simulink reference outputs
- Python twin outputs
- Candidate controllers
- MATLAB or MATLAB Engine configuration

## Outputs

- Validation tables and plots
- Error and agreement metrics
- Final Simulink confirmation results
- Identified model limitations

## Verification Steps

- Confirm initial and terminal conditions match.
- Check time-base, energy, current, and sign conventions.
- Repeat core validation scenarios with the same seeds.
- Record unavailable or unavailable-Simulink cases explicitly.
- Compare at least one baseline and one LLM candidate in the final check.
