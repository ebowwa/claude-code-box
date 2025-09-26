# Claude Code Box

## Overview

The Claude Code project provides both a Python SDK and a Node.js-based CLI that enable seamless access to Anthropic's Claude models for development, automation, and integration workflows. The CLI depends on Node.js 18+ and is designed to run interactively or in headless environments, while the Python SDK offers programmatic control suitable for scripting, notebooks, or server-side automation. Together they support local development, CI pipelines, and production deployments by unifying access patterns to Claude's tool-calling and text-generation capabilities.

## Requirements

- **Claude Code CLI**: Requires Node.js 18 or later. Installation happens via `npm` and the CLI must be available on the system `PATH` for SDK interoperation.
- **Claude Code Python SDK**: Requires Python 3.9 or later. The SDK relies on the Claude Code CLI for transport, so ensure the CLI is installed before importing or invoking SDK features.
- **Anthropic/Z.AI credentials**: Provide API keys and model routing configuration through environment variables when running in local, headless, or CI environments.

## Installation

Install the CLI globally via npm:

```bash
npm install -g @anthropic-ai/claude-code
```

Install the Python SDK (after installing the CLI):

```bash
pip install claude-code-sdk
```

> **Note:** The Python SDK shells out to the Claude Code CLI for some operations. Ensure `claude` is on your `PATH` before using the SDK.

## Headless and CI Usage

The CLI and SDK can run non-interactively for automation. For headless usage:

- Set the required API credentials via environment variables in your CI system or container.
- Invoke the CLI with scripts or piping input/output to integrate with other tools.
- Use the Python SDK within automated jobs (e.g., GitHub Actions, Jenkins) to orchestrate multi-turn conversations or tool invocations without manual prompts.

Refer to the [Claude Docs: Headless mode](https://docs.anthropic.com/claude/docs/headless-mode) for additional patterns and configuration tips.

## Model and Credential Configuration

Configure the runtime via environment variables recognized by the CLI and SDK:

- `ANTHROPIC_API_KEY` or `ANTHROPIC_AUTH_TOKEN` for authentication.
- `ANTHROPIC_MODEL` to choose the target model family.
- `ANTHROPIC_BASE_URL` when routing through an Anthropic-compatible gateway such as Z.AI.

The upstream `ANTHROPIC_SMALL_FAST_MODEL` variable is deprecated—confirm the recommended replacement in the latest Anthropic and Z.AI documentation. See the [Claude Docs on model configuration](https://docs.anthropic.com/claude/docs/model-configuration) for current guidance.

## Routing Through Gateways

When using Anthropic-compatible gateways (e.g., Z.AI), configure the CLI and SDK with the gateway base URL and authentication token. Gateways can enable data residency, caching, or quota management. Review the [Anthropic LLM Gateway guide](https://docs.anthropic.com/claude/docs/llm-gateway) and the [Z.AI Anthropic compatibility documentation](https://docs.z.ai/docs/anthropic) for specifics on endpoints, headers, and supported models.

## Python Usage Examples

### Single-turn Query

```python
from claude_code_sdk import query

response = query(
    prompt="Generate a Python function that adds two numbers.",
    model="claude-3-opus-20240229",
)
print(response.completion)
```

### Multi-turn Session with Tools

```python
from claude_code_sdk import ClaudeSDKClient
from claude_code_sdk.tools import MCPToolRegistry

# Assume the CLI and MCP tools are already configured on the system.
registry = MCPToolRegistry.from_cli()

client = ClaudeSDKClient(
    model="claude-3-opus-20240229",
    tools=registry.list_tools(),
)

with client.new_session() as session:
    session.send("Scan the repository and suggest improvements.")
    session.send("Use the python_repl tool to show a quick example.")
    result = session.get_transcript()

print(result)
```

### Environment-based Configuration (Z.AI)

```python
import os
from claude_code_sdk import ClaudeSDKClient

os.environ["ANTHROPIC_BASE_URL"] = "https://api.z.ai/anthropic"
os.environ["ANTHROPIC_MODEL"] = "claude-3-sonnet-20240229"
os.environ["ANTHROPIC_AUTH_TOKEN"] = "your-z-ai-token"

client = ClaudeSDKClient()
response = client.query("Summarize the release notes for the latest SDK update.")
print(response.completion)
```

Ensure you verify environment variable names and behavior against the latest Anthropic and Z.AI references, as upstream deprecations (e.g., `ANTHROPIC_SMALL_FAST_MODEL`) may affect compatibility.

## Additional Resources

- [Anthropic official SDK repository](https://github.com/anthropics/anthropic-sdk-python)
- [Claude Docs: Headless mode](https://docs.anthropic.com/claude/docs/headless-mode)
- [Claude Docs: Model configuration](https://docs.anthropic.com/claude/docs/model-configuration)
- [Claude Docs: LLM Gateway](https://docs.anthropic.com/claude/docs/llm-gateway)
- [Z.AI Anthropic compatibility documentation](https://docs.z.ai/docs/anthropic)

