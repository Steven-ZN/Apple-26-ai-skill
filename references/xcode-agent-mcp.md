# Xcode Agent MCP

Use this reference when setting up Claude Code, Codex, Cursor-style agents, or Xcode coding intelligence for Apple platform projects.

## Xcode Coding Intelligence

Xcode supports chat and agent products for coding assistance. Agent products can modify projects and use Xcode capabilities such as building, testing, and searching Apple documentation after the user grants permission.

When working inside Xcode:

- Enable the desired agent or chat provider in `Xcode > Settings > Intelligence`.
- Use Claude Agent or Codex for autonomous build/test/fix workflows.
- Use ChatGPT or Claude chat when the user wants reviewable suggestions instead of agentic edits.
- Configure provider account or API key through Xcode settings, not project source.

## External Agent Access Through MCP

External agents can connect to Xcode through the MCP server exposed by Xcode. Require the user to open the project in Xcode first.

Setup outline:

1. In Xcode, open `Settings > Intelligence`.
2. Under Model Context Protocol, enable external agents to use Xcode tools.
3. Configure the external agent with `xcrun mcpbridge`.

Known command forms from Apple documentation:

```bash
claude mcp add --transport stdio xcode -- xcrun mcpbridge
codex mcp add xcode -- xcrun mcpbridge
```

Verify with:

```bash
claude mcp list
codex mcp list
```

## Agent Config Locations

For agents launched inside Xcode, use Xcode-specific configuration folders:

```text
~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig
~/Library/Developer/Xcode/CodingAssistant/codex
```

Use these for default model settings, additional MCP servers, and skills that should apply only to agents launched from Xcode.

For external agents launched from a repository, prefer repository-local files:

```text
AGENTS.md
CLAUDE.md
```

Add concise project hints:

- How to open/build/test the project.
- Preferred simulator/device.
- Known schemes.
- Where generated code should and should not go.
- Availability and privacy rules for AI features.

## Permissions

Use the narrowest useful permissions.

- Allow Xcode tools needed for build/test/preview/doc lookup.
- Add command-line tools only when the workflow requires them.
- Remove MCP tools or commands after one-off sensitive work.
- Do not grant broad shell permissions just to make an AI feature compile.

## Verification Checklist

- Xcode project is open.
- Correct scheme and destination are selected.
- MCP bridge is listed by the external agent.
- Agent can see Xcode tools.
- First build/test action asks for expected permissions.
- The repo has agent hints describing build/test commands and AI feature constraints.
