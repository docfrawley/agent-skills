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

## Claude Code and MCP inventory

Where accessible, enumerate:

- `.mcp.json`
- `.claude/settings.json`
- `.claude/settings.local.json`
- `.claude/agents/`
- `.claude/hooks/`
- `.claude/skills/`
- project plugins
- `~/.claude.json`
- `~/.claude/settings.json`
- Claude desktop configuration where present
- installed Claude Code plugins and skills

For each agent definition, inspect tool permissions, wildcard grants, shell access, filesystem reach, MCP access, and whether permissions are project-scoped or global.

For each MCP server record:

- server name
- local / remote
- project-scoped / user-global
- command or endpoint
- connected systems
- credential or identity mechanism
- tools exposed
- read / write / destructive capability
- network reach
- whether authorization is externally enforced or merely described
- evidence confidence

## Hooks, skills, and plugins

Hooks may execute commands on the host outside the conversational permission model. Record trigger, command/script, host permissions, inherited environment, network capability, and secrets potentially visible to the process.

Inspect installed skills/plugins for instructions or scripts that execute shell commands, access sensitive paths, fetch URLs, invoke MCP servers, read environment variables, modify files outside the project, or install packages/binaries.

Do not assume an installed skill or plugin is trusted merely because it is present.
