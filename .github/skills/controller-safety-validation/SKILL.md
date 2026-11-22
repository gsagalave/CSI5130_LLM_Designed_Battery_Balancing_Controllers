---
name: controller-safety-validation
description: Validate generated or tuned battery controllers against current, voltage, temperature, and code-safety limits.
---

# Controller Safety Validation

Use this skill for safety wrappers and rejection logic in `optimizer/` and `twin/`.

## Instructions

- Treat every LLM-generated action as untrusted.
- Clamp balancing and pack currents to configured safe limits.
- Validate voltage, SOC, temperature, and current bounds before and after integration.
- Reject code that imports or calls prohibited modules or uses uncontrolled execution patterns.
- Define a clear distinction between clamped unsafe output and a controller that caused a simulation violation.
- Preserve violation details for reproducibility and debugging.
- Prevent threshold oscillation or excessive switching when operating near a threshold.

## Workflow

1. Define battery limits and controller-output schema.
2. Syntax-check and statically validate generated source.
3. Execute the controller in the twin's constrained interface.
4. Clamp or reject invalid outputs based on a documented policy.
5. Verify state and limits after every simulation step.
6. Return a safety record alongside performance metrics.

## Inputs

- Controller code or parameter vector
- Safe current, voltage, SOC, and temperature ranges
- Maximum switching or step limits
- Simulation state and time

## Outputs

- Accepted or rejected status
- Clamped action count
- Violation category and step
- Safe action trace
- Human-readable rejection reason

## Verification Steps

- Test negative, excessive, NaN, and infinite values.
- Test missing, extra, and malformed controller outputs.
- Verify that prohibited code imports are blocked.
- Verify that no violation is silently converted into a success result.
- Re-run a failed controller with a stricter wrapper and confirm it cannot bypass limits.
