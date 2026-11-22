---
name: bms-copilot
description: Build and operate a ReAct-style battery management copilot grounded in simulation tools and measured results.
---

# BMS Copilot

Use this skill for chat tools and UI work under `agent/`.

## Instructions

- Answer questions through available simulation, comparison, and plotting tools rather than unsupported guesses.
- Cite or report the scenario, controller, metric, and result set used for each claim.
- Distinguish measured results, model assumptions, and interpretation.
- Support questions such as why the rule-based controller did not finish, which design is faster, or which trades off less heat.
- Build a small 30–50-question evaluation set with known answers from generated evidence.
- Avoid presenting estimated Simulink agreement as exact unless validation has established it.

## Workflow

1. Parse the user's battery or experiment question.
2. Select a relevant tool or bounded repository data source.
3. Run or retrieve real scenario data.
4. Compare and format the evidence.
5. Generate a short explanation and optional plot.
6. State uncertainty, missing evidence, or safety caveats.

## Inputs

- Natural-language question
- Available simulation and result tools
- Scenario or experiment identifiers
- Optional plot or comparison request

## Outputs

- Grounded answer
- Result data or plot
- Evidence and assumptions
- Tool invocation trace
- Confidence or limitation note

## Verification Steps

- Test each copilot tool with known 4-cell results.
- Run the answer-question set and record accuracy and unsupported claims.
- Confirm response handling for unknown scenario names and missing results.
- Verify plotted values match source result records.
- Measure response time and API cost where applicable.
