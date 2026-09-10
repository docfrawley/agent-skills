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
- managed settings visible to the local installation
- Claude desktop configuration where present
- installed Claude Code plugins and skills

Inspect the effective permission/configuration surface, not only the file where a setting was first found. In current Claude Code versions this commonly includes:

- `permissions.allow`
- `permissions.ask`
- `permissions.deny`
- `permissions.defaultMode`, especially `bypassPermissions`
- `permissions.additionalDirectories`
- `enableAllProjectMcpServers`
- `enabledMcpjsonServers`
- CLI/session overrides such as `--allowedTools`, `--disallowedTools`, `--permission-mode`, and `--add-dir` when observable

Because settings can merge across scopes and product keys can evolve, verify effective behavior against the installed/current Claude Code configuration rather than assuming one file is authoritative. These names are version-sensitive examples rather than a schema, and a key that is absent is not a finding — see `platform-checks.md`, which states the rule and the underlying capabilities to audit instead.

For each agent definition, inspect tool permissions, wildcard grants such as broad `Bash(...)` patterns, shell access, filesystem reach, MCP access, and whether permissions are project-scoped, user-scoped, or managed.

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

Hooks may execute commands, HTTP requests, prompts, or MCP tools automatically at lifecycle events. Record event, matcher, handler type, command/endpoint/tool, host permissions, inherited environment, network capability, and secrets potentially visible to the process.

Pay particular attention to lifecycle hooks that execute before or around normal user interaction or tool authorization, including current events such as:

- `SessionStart`
- `Setup`
- `UserPromptSubmit`
- `PreToolUse`
- `PermissionRequest`
- `PostToolUse`
- `ConfigChange`
- `InstructionsLoaded`

`SessionStart` deserves special attention because it runs when sessions begin or resume and can inject context or persist environment variables before ordinary work proceeds.

Inspect installed skills/plugins for instructions or scripts that execute shell commands, access sensitive paths, fetch URLs, invoke MCP servers, read environment variables, modify files outside the project, or install packages/binaries.

Where supported by the installed version, note settings that suppress skill-initiated shell execution, such as `disableSkillShellExecution`, and whether they are enforced from a scope users cannot override.

Do not assume an installed skill, plugin, hook, or MCP server is trusted merely because it is present.
