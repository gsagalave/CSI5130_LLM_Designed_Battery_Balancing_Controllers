---
name: battery-digital-twin
description: Implement and maintain the fast Python battery pack simulator for cell balancing research.
---

# Battery Digital Twin

Use this skill for work under `twin/` or for implementing simulation, state, and metric logic.

## Instructions

- Model a series-connected pack with configurable cell count, capacity, internal resistance, initial state of charge, load, and converter behavior.
- Support 4, 8, and 12-cell scenarios and permit heterogeneous cells.
- Use average currents and coarse time steps; do not model individual switching pulses.
- Keep the model fast enough for thousands of controller evaluations.
- Separate scenario generation, state integration, controller execution, and metric calculation.
- Define units explicitly and preserve consistent conventions for current, voltage, charge, energy, power, and heat.
- Include pack, cell, and failure-level metrics needed by the controller designer and evaluation tools.

## Workflow

1. Read existing twin and baseline interfaces before changing equations or result fields.
2. Implement or update the validated equations and numerical integration.
3. Generate deterministic design and held-out test scenarios.
4. Run the smallest unit or smoke test covering state updates and metrics.
5. Export machine-readable scenarios and results for evaluation and plotting.

## Inputs

- Pack and cell parameters
- Initial SOC or charge values
- External load and operating mode
- Controller callback or controller parameters
- Time step and maximum simulation duration
- Terminal and completion thresholds

## Outputs

- Final and time-series cell SOC or voltage
- Balancing time
- Total energy loss
- Cell heat and I-squared-R loss
- Peak and RMS cell currents
- Completion, timeout, and safety status
- Reproducible scenario identifier and configuration

## Verification Steps

- Check unit consistency and numerical stability.
- Verify conservation or loss accounting within a documented tolerance.
- Test nominal, heterogeneous, loaded, and worst-imbalance scenarios.
- Confirm that balancing completion thresholds and timeouts are applied consistently.
- Confirm that the fast model's interface matches the Simulink comparison workflow.
