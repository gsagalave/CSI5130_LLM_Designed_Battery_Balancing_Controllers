---
name: llm-controller-designer
description: Design and improve battery balancing controllers using LLM tuning or code-generation feedback loops.
---

# LLM Controller Designer

Use this skill for work under `optimizer/` that asks an LLM to tune parameters or write balancing controller code.

## Instructions

- Support two modes:
  - **Tuning mode:** score prior parameter vectors and request the next improved candidate, following an OPRO-style history prompt.
  - **Code mode:** request a short, readable Python controller and evaluate each complete generated program.
- Pass only documented inputs to the generated controller, such as SOC, voltage, time, and pack limits.
- Never execute untrusted code outside a constrained runner or without validation.
- Preserve every attempt, prompt, candidate, result, and failure reason.
- Report simulation feedback in concise numeric and textual form.
- Reward improvement while preventing score hacking and unsafe behavior.
- Keep generated controllers explainable and free of hard-coded scenario-specific results.

## Workflow

1. Load the controller interface and design-scenario configuration.
2. Define a single reproducible objective or Pareto fitness function.
3. Generate a tuning vector or Python candidate.
4. Syntax-check and execute the candidate through the safety wrapper.
5. Run the candidate on the design scenarios.
6. Record metrics and safety status.
7. Send measured feedback to the next design round.
8. Retain Pareto-eligible and best-overall candidates for later evaluation.

## Inputs

- Model interface and allowed observations
- Simulation metrics and safety limits
- Prior candidate history
- Optimization budget
- Prompt or physics-context choice
- Model and generation settings

## Outputs

- Generated parameter vector or controller source
- Per-attempt metrics
- Design-round history
- Best candidates and explanations
- Invalid, unsafe, timed-out, and failed-attempt records

## Verification Steps

- Test parsing and execution of valid, invalid, and unsafe generated code.
- Confirm fixed candidate and scenario budgets.
- Include a known simple controller in the smoke test.
- Verify that a no-progress or failure case terminates rather than looping indefinitely.
- Compare tuning mode and code mode under identical scenario and attempt budgets.
