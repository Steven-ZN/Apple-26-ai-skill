# Dynamic Profile, Profiling, and Evaluation

Use this reference for Apple Foundation Models, PCC, Core AI, MLX, or third-party model features that need profiling, evaluation, or multi-session debugging.

## Terminology

Use Apple's Dynamic Profile terminology for versioned runtime model configurations used in profiling and evaluating agentic Foundation Models experiences. Apple materials also refer to profiling agentic app experiences, multiple `LanguageModelSession`s, profiles, Dynamic Instructions, and tool call loops.

A Dynamic Profile is a versioned runtime configuration:

- Model/provider.
- Instructions.
- Prompt template.
- Output schema.
- Tools enabled.
- Input modalities.
- Context sources.
- Privacy mode.
- Availability conditions.
- Fallback path.
- Eval fixture set.
- Profiling target.

## Profile Matrix Template

```markdown
| Profile | Provider | Instructions | Schema | Tools | Inputs | Privacy | Fallback | Eval Set | Metrics |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| summarize-v1-local | Foundation Models on-device | summarize-v1 | SummaryCard.v1 | none | text | on-device | no-AI summary | fixtures/summarize | TTFT, total latency, refusal rate |
| summarize-v1-pcc | PCC | summarize-v1 | SummaryCard.v1 | none | text+image | Apple PCC | local text-only | fixtures/summarize | TTFT, tokens/sec, quality score |
```

Keep Dynamic Profiles in source control when they affect product behavior. Use JSON, YAML, Swift constants, or test fixtures according to the repo's existing patterns.

## Instruments Profiling

Use the Foundation Models or Core AI Instruments templates when available.

Inspect:

- Prompts and instructions.
- Session/request/inference boundaries.
- Tool calls and tool outputs.
- Multi-session handoffs.
- Time-to-first-token.
- Tokens per second.
- Total latency.
- First-run model specialization.
- Warm-run model performance.

Privacy caution:

- Trace files can contain sensitive prompt/user data.
- Do not commit traces.
- Redact or regenerate traces with synthetic inputs before sharing.

## Evaluation Workflow

1. Select 5-20 representative inputs before changing prompts or models.
2. Record the active Dynamic Profile.
3. Generate outputs with the old profile.
4. Generate outputs with the new profile.
5. Score deterministically where possible:
   - Schema validity.
   - Required fields present.
   - No hallucinated entities.
   - Tool calls correct and minimal.
   - Locale/language preserved.
   - Safety/refusal behavior acceptable.
6. Use model-as-judge only as a secondary signal.
7. Keep failures as regression fixtures.

## Prompt and Tool Loop Debugging

When an agentic flow misbehaves:

- Reduce to one profile and one fixture.
- Confirm instructions are not contradictory.
- Check whether tool descriptions invite broad or unsafe actions.
- Inspect transcript order: instructions, prompt, tool call, tool output, response.
- Verify tool output is concise enough for context.
- Add guardrails for maximum tool iterations.
- Prefer typed tool arguments over free-form strings.

## Performance Targets

Define targets per feature, not globally.

Useful metrics:

- TTFT: reduce by shortening instructions, prewarming, or changing provider.
- Tokens/sec: compare across model profiles and devices.
- Total latency: reduce with streaming, smaller schemas, fewer tools, or local fallback.
- First-run latency: use AOT compile or explain one-time setup in UX.
- Fallback rate: track model unavailability and timeout separately.

## Done Criteria

An AI feature is not done until:

- At least one happy-path eval fixture passes.
- At least one fallback path is tested.
- Structured output parsing is covered.
- Privacy mode is documented in code or product copy.
- Profiling plan or observed metrics are recorded.
- Sensitive traces and raw prompts are not committed.
