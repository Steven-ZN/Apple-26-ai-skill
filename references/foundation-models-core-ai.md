# Foundation Models, PCC, Core AI, and Local Model Selection

Use this reference when choosing or implementing Apple AI runtime support.

## Model Selection

Prefer the smallest runtime that satisfies the product requirement.

| Need | Prefer | Notes |
| --- | --- | --- |
| Apple-native language feature, structured output, tool calling | Foundation Models | Keep output typed and test prompts. |
| Stronger Apple-hosted model, vision, higher capability | Private Cloud Compute | Check availability and provide offline fallback. |
| Bundled on-device open-source/custom model | Core AI | Handle model size, specialization, memory, and AOT compile. |
| Local Apple silicon experimentation | MLX | Good for prototypes and local model workflows. |
| Provider-specific capability or existing backend | Third-party provider | Secure credentials and disclose cloud processing. |
| Shell/Python automation or evals | `fm` CLI / Python SDK | Verify installed OS/toolchain support first. |

## Foundation Models Patterns

Use Foundation Models for:

- `LanguageModelSession` style interactions.
- Structured output with typed models such as `@Generable`.
- Tool calling.
- Guided generation.
- Streaming responses.
- Multi-session agentic flows.
- Context management and semantic search when available in the installed SDK.

Implementation guidance:

- Define the output type before writing the prompt.
- Keep instructions short, stable, and versioned.
- Include schema version or prompt version in test fixtures.
- Keep tool descriptions specific and side-effect boundaries explicit.
- Fail closed when a model lacks a requested capability.

Error handling to plan for:

- Context window exceeded.
- Rate limited.
- Refusal.
- Guardrail violation.
- Unsupported capability.
- Unsupported transcript content.
- Unsupported generation guide.
- Unsupported language or locale.
- Timeout.

## Private Cloud Compute

Use PCC when the feature needs more capability than the on-device model and Apple-hosted privacy properties are required.

Required behavior:

- Check availability before creating a PCC-backed flow.
- Provide a fallback to on-device, no-AI, or "try later" behavior.
- Make network state visible in UX when latency or availability matters.
- Test PCC and fallback flows separately.

## Custom LLM Provider Integration

For custom providers, implement the provider abstraction rather than scattering provider calls through app code.

Expected concepts:

- `LanguageModel` declares capabilities and executor configuration.
- `LanguageModelExecutor` maps transcript entries to provider-native messages.
- `prewarm` loads reusable model/provider state where appropriate.
- `respond` streams text, usage, metadata, and custom segments.
- Throw framework errors when the provider cannot honor the developer's requested capability.

Security:

- Do not accept raw API keys in view code.
- Prefer Keychain or a secure auth flow.
- For cloud model services, consider device attestation where applicable.

## fm CLI and Python SDK

Use the `fm` CLI for repeatable shell workflows and quick structured outputs.

Patterns:

```bash
fm respond "Summarize this Swift compile error and suggest the smallest fix"
fm schema object --name Result --string summary --string risk > schema.json
fm respond "Classify these files" --schema schema.json
```

Use `--model pcc` and `--image` only when the installed `fm` command supports them.

Use the Python SDK for:

- Prompt evaluation pipelines.
- Jupyter or Pandas analysis.
- Batch tests over prompt/profile variants.
- Tool calling prototypes before porting to Swift.

## Core AI

Use Core AI for on-device model deployment on Apple silicon.

Apple describes Core AI as a new Apple silicon-focused framework with Swift APIs, Python tooling, `.aimodel` assets, Xcode integration, Instruments profiling, model specialization, and ahead-of-time compilation. Treat exact API signatures as beta/preliminary until verified against the installed Xcode SDK.

Workflow:

1. Choose a Core AI model package or convert a model to `.aimodel`.
2. Inspect model metadata, supported platforms, functions, and tensor shapes.
3. Add Swift package/runtime dependency.
4. Load the model and call a typed wrapper when available.
5. Profile specialization latency, memory, and warm-run performance.
6. Use ahead-of-time compilation when first-run specialization is too slow.

Known command form:

```bash
xcrun coreai-build compile MyModel.aimodel --platform iOS
```

Failure modes:

- Asset missing or incompatible with platform.
- First-run specialization takes too long.
- Model does not fit memory budget.
- Tensor shape mismatch.
- Accuracy drift after conversion or optimization.

Always test converted model numerics against the source model when feasible.
