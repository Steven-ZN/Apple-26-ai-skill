---
name: apple-26-ai-skill
description: Implement the Apple developer AI and coding-agent features introduced or expanded around WWDC2026 in Xcode projects. Use when building, testing, debugging, or profiling iOS, iPadOS, macOS, visionOS, watchOS, Swift, or SwiftUI apps that involve Xcode coding intelligence, external agent access through Xcode MCP, Claude Agent, Codex, Apple Foundation Models, Private Cloud Compute, custom LLM providers, Core AI, MLX, fm CLI, Foundation Models Python SDK, Instruments profiling, Apple Dynamic Profiles, availability fallbacks, or privacy-sensitive AI feature implementation.
---

# Apple 26 AI Skill

Use this skill to implement Apple developer AI and coding-agent capabilities introduced or expanded around WWDC2026. It turns the WWDC2026 feature set into concrete coding-agent workflows for Xcode projects.

## WWDC2026 Feature Set

Implement or integrate:

- Xcode coding intelligence for chat and agent workflows.
- External agent access to Xcode through Xcode MCP and `xcrun mcpbridge`.
- Claude Agent and Codex workflows inside Xcode projects.
- Apple Foundation Models app features: structured output, guided generation, tool calling, streaming, context handling, and multi-session agentic flows.
- Private Cloud Compute-backed Apple Foundation Model flows with availability and fallback handling.
- Custom LLM providers through Foundation Models provider abstractions.
- `fm` CLI and Foundation Models Python SDK workflows for scripting, prototyping, and evaluation.
- Core AI on-device model deployment with `.aimodel` assets, model specialization, ahead-of-time compilation, Xcode inspection, and Instruments profiling.
- MLX or local Apple silicon model workflows when appropriate.
- Apple Dynamic Profiles and Instruments workflows for comparing model, prompt, tool, schema, privacy, fallback, and latency behavior.

## Core Workflow

1. Identify the project type and target platforms.
   - Inspect `Package.swift`, `.xcodeproj`, `.xcworkspace`, deployment targets, entitlements, and existing SwiftUI/UIKit/AppKit patterns.
   - Confirm whether the task is app feature implementation, model integration, agent tooling setup, or profiling.

2. Connect the agent to Xcode when useful.
   - For build/test/preview/device actions, use Xcode directly if available.
   - For external agents, follow [xcode-agent-mcp.md](references/xcode-agent-mcp.md).
   - Do not assume Xcode MCP is configured; verify before relying on it.

3. Choose the AI runtime deliberately.
   - Use Foundation Models for Apple-native language features, structured output, guided generation, tool calling, and app-integrated sessions.
   - Use Private Cloud Compute when the feature needs stronger server-backed Apple Foundation Model capability and network availability is acceptable.
   - Use Core AI for bundled or downloaded on-device models, `.aimodel` assets, and zero-token-cost local inference.
   - Use MLX for local/open-source model experimentation on Apple silicon when the app or tooling already depends on MLX.
   - Use third-party provider integration only when the product requirements justify cloud dependency, credentials, or model-specific capabilities.
   - For details, read [foundation-models-core-ai.md](references/foundation-models-core-ai.md).

4. Implement with availability, fallback, and privacy in the first pass.
   - Add platform availability checks before using new frameworks or model types.
   - Provide graceful fallback for unavailable model, unsupported locale, offline state, rate limits, refusal, timeout, or guardrail errors.
   - Keep user-visible copy honest about on-device, Private Cloud Compute, and third-party/cloud processing.
   - Never hardcode API keys in source. Prefer Keychain, secure configuration, or provider-recommended auth.

5. Make outputs testable.
   - Prefer typed structured output (`@Generable` or equivalent) over ad hoc JSON parsing.
   - Add regression inputs for prompts, schemas, fallback branches, and tool-calling paths.
   - For Python or CLI experiments, use `fm` / Foundation Models Python SDK only when available on the host OS and documented by the installed toolchain.

6. Profile dynamic AI behavior before calling the feature done.
   - Define an Apple Dynamic Profile for each model/instructions/tools/schema/fallback combination.
   - Trace multi-session agentic flows, tool loops, prompts, and latency with Instruments when possible.
   - Read [dynamic-profiling-evaluation.md](references/dynamic-profiling-evaluation.md).

## Implementation Rules

- Verify API names against the installed Xcode SDK, Apple documentation, or generated interface before writing substantial code.
- Do not invent missing API details; use the installed SDK and Apple Developer references for exact symbols.
- Keep Swift code idiomatic for the existing app: Swift concurrency, `@Observable`/SwiftData/SwiftUI patterns only when already adopted or clearly warranted.
- For app AI features, include UI states for loading, partial/streaming output, unavailable model, refused request, and retry.
- For agentic features, log enough non-sensitive metadata to debug control flow, but do not persist raw prompts, screenshots, or user content unless product requirements and privacy policy permit it.

## Common Tasks

### Add a Foundation Models Feature

1. Confirm target OS and framework availability.
2. Define the user experience and output contract before prompting.
3. Create typed output models when possible.
4. Add model selection and fallback path.
5. Add tests or repeatable eval cases for representative inputs.
6. Add profiling notes for TTFT, tokens/sec, total latency, tool calls, and fallback rate.

### Add or Review Xcode Agent Setup

1. Confirm the user wants Xcode to expose project/build/test capabilities to the agent.
2. Enable Xcode Intelligence permissions and MCP only where appropriate.
3. Configure external agents using `xcrun mcpbridge`.
4. Put agent-specific project hints in `AGENTS.md`, `CLAUDE.md`, or the Xcode CodingAssistant config folder as appropriate.
5. Restrict command-line and MCP tool permissions to what the task needs.

### Add Core AI On-Device Model Support

1. Decide whether to use a prebuilt Core AI model package or convert a model.
2. Inspect model size, platform targets, function signatures, and tensor shapes in Xcode when available.
3. Compile ahead of time when first-run specialization latency would harm UX.
4. Add loading, specialization, cache, and memory failure handling.
5. Profile first run and warm run separately.

### Build a Dynamic Profile Matrix

Create or update a table with:

- Profile name and purpose.
- Model/provider: on-device Foundation Model, PCC, Core AI, MLX, or third-party.
- Instructions and schema version.
- Tools enabled.
- Input modalities.
- Availability constraints.
- Privacy mode.
- Fallback path.
- Eval dataset or fixtures.
- Profiling target and observed metrics.

## Reference Files

- [xcode-agent-mcp.md](references/xcode-agent-mcp.md): Xcode coding intelligence, Claude/Codex setup, MCP bridge, and permissions.
- [foundation-models-core-ai.md](references/foundation-models-core-ai.md): Foundation Models, PCC, provider integration, `fm`, Python SDK, Core AI, and model selection.
- [dynamic-profiling-evaluation.md](references/dynamic-profiling-evaluation.md): Dynamic profiles, Instruments tracing, eval pipelines, and performance metrics.
- [apple-developer-links.md](references/apple-developer-links.md): Apple Developer reference links.
