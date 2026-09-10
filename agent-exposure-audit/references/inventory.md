# Inventory Procedure

Read this reference when directed by `SKILL.md` for inventory and enumeration.

## Application dependencies

Inspect dependency manifests and lockfiles.

### Python

Check `pyproject.toml`, `requirements*.txt`, `poetry.lock`, `uv.lock`, `Pipfile`, `Pipfile.lock`, and dependency files used by notebooks or services.

Search for AI/agent dependencies including `langchain`, `langflow`, `langgraph`, `llama_index`, `llamaindex`, `crewai`, `autogen`, `semantic-kernel`, `haystack`, `dspy`, `instructor`, `guidance`, `litellm`, `mcp`, `fastmcp`, `openai`, `anthropic`, `google-generativeai`, `ollama`, `transformers`, and `vllm`.

### JavaScript / TypeScript

Check `package.json`, `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `bun.lock`, and workspace manifests.

Search for `@langchain/*`, `@modelcontextprotocol/sdk`, `@anthropic-ai/sdk`, `openai`, Vercel `ai`, and other packages clearly serving model, agent, orchestration, tool-use, or MCP roles.

Do not rely solely on these lists. Search imports and dependency names for additional relevant runtimes.

For each component record:

- name and version
- pinned / constrained / floating
- declaration location
- import/use location
- production / development / test / notebook use
- network-exposed or local-only
- process/service that runs it
- owner, if identifiable
- evidence confidence

## Runtime surfaces

Inspect surfaces that may execute or expose AI components:

- `Dockerfile*`
- `docker-compose*.yml`
- Kubernetes manifests
- Helm charts
- Terraform
- CloudFormation
- Pulumi
- `Procfile`
- systemd units
- serverless configuration
- deployment scripts
- CI/CD workflows

When current vulnerability data is available, cross-check identified versions against known vulnerabilities and known-exploited vulnerabilities. Prioritize active exploitation, unauthenticated remote access, credential/code-execution exposure, and downstream reach. Do not rank solely by CVSS.

## Agent-harness inventory

Identify the active coding-agent or agent-runtime environment where possible. Examples include Claude Code, Codex, Cursor, Pi, and other Agent Skills-compatible harnesses.

Read `../harnesses/README.md`, then the matching adapter if one exists.

For every harness inspect, where evidence permits:

- project-, user-, and managed/global instruction sources
- skill locations and discovery paths
- effective tool permissions
- filesystem reach outside the repository
- shell/terminal authority
- network/browser/web authority
- MCP or other external tool-server configuration
- hooks, lifecycle automation, or pre/post tool execution
- agents, subagents, delegation, or background workers
- plugins/extensions/packages that add authority
- approval, sandbox, or permission-bypass modes
- CLI/session overrides
- inherited environment variables and ambient credentials
- project trust or workspace trust state

Determine the **effective result after scope merging and runtime overrides**, not merely the value in one configuration file.

Product-specific names are version-sensitive examples, not security facts. Absence of a named key is not a finding. Audit the underlying capability.

## Tool-server / MCP inventory

For each MCP or equivalent tool server record:

- server name
- transport and local / remote status
- project-scoped / user-global / managed scope
- command or endpoint
- connected systems
- credential or identity mechanism
- tools exposed
- read / write / destructive capability
- network reach
- whether authorization is externally enforced or merely described by the client
- evidence confidence

## Hooks, skills, plugins, and extensions

Hooks or lifecycle automation may execute commands, HTTP requests, prompts, or tool calls automatically. Record event/trigger, matcher, handler type, command/endpoint/tool, host permissions, inherited environment, network capability, and secrets potentially visible to the process.

Inspect installed skills/plugins/extensions for instructions or scripts that:

- execute shell commands
- access sensitive paths
- fetch URLs or use browsers/network tools
- invoke MCP/tool servers
- read environment variables or credential stores
- modify files outside the project
- install packages/binaries
- delegate to another agent or process with broader authority

Do not assume an installed skill, plugin, hook, extension, or tool server is trusted merely because it is present.
