---
name: openrouter-llm-comparison
description: Compare controller-design LLMs consistently through OpenRouter while tracking quality, cost, latency, and reproducibility.
---

# OpenRouter LLM Comparison

Use this skill for model API calls, model comparison experiments, and LLM cost or latency measurement.

## Instructions

- Use a shared prompt, scenario set, attempt budget, and controller evaluator for every model.
- Store API keys only in environment variables or a secret provider, never in source files or logs.
- Record model identifier, prompt version, temperature, token counts, latency, cost, and API response status.
- Catch invalid JSON or code and record it as a failed attempt, not as a success.
- Support GPT, Claude, Gemini, and open-source model identifiers through the project's provider configuration.
- Use deterministic seeds where supported and disclose provider or sampling nondeterminism.

## Workflow

1. Validate environment configuration and API credentials.
2. Load a versioned model and prompt manifest.
3. Run each model under the same design and safety pipeline.
4. Record usage, timing, failures, and generated candidates.
5. Aggregate quality, invalid-output rate, cost, and latency.
6. Export fair-comparison summaries.

## Inputs

- OpenRouter model identifiers
- API credentials and environment configuration
- Prompt templates and prompt versions
- Shared attempt and scenario budgets
- Design-scenario and safety configuration

## Outputs

- Model-comparison records
- Generated candidates and scores
- Invalid-code or unsafe-output counts
- Cost and response-time summaries
- Reproducible manifest

## Verification Steps

- Run a one-attempt, low-cost smoke test for each configured model.
- Confirm the API key never appears in logs or result files.
- Verify equal attempt and scenario budgets across models.
- Test malformed, empty, and fenced-code responses.
- Recheck cost and token fields against provider usage data when available.
