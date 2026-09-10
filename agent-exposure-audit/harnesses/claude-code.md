# Claude Code Adapter

Use this adapter when Claude Code is the active harness.

Configuration names are version-sensitive examples. Verify against the installed/current Claude Code version when possible.

## Discovery

Inspect, where accessible:

- `.mcp.json`
- `.claude/settings.json`
- `.claude/settings.local.json`
- `.claude/agents/`
- `.claude/hooks/`
- `.claude/skills/`
- project plugins
- `~/.claude.json`
- `~/.claude/settings.json`
- managed settings visible to the installation
- installed Claude Code plugins and skills

Determine the effective result after scope/precedence/merging, not merely the value in one settings file.

## Permission and reach checks

Current/common control surfaces may include:

- `permissions.allow`
- `permissions.ask`
- `permissions.deny`
- `permissions.defaultMode`, especially unrestricted/bypass modes
- additional filesystem directories
- project MCP enablement/approval controls
- CLI/session overrides that alter allowed/disallowed tools, permission mode, or filesystem reach

Treat broad `Bash(...)` authorization or unrestricted shell as mutation-capable unless the command pattern itself constrains behavior to read-only inspection.

## Hooks and automatic execution

Inspect hooks by lifecycle event, matcher, handler type, and authority. Pay particular attention to hooks that execute before or around normal user interaction, permission decisions, or tool invocation.

Examples in current/recent versions have included `SessionStart`, `Setup`, `UserPromptSubmit`, `PreToolUse`, `PermissionRequest`, and `PostToolUse`. Verify actual events against the installed version.

For startup/session hooks, inspect whether they:

- inject content into model context
- persist or modify environment variables
- inherit credentials before normal interaction begins
- execute shell or network actions automatically
- originate from project, user, plugin, or managed scope

## Skills, agents, plugins, and MCP

Inspect:

- agent tool grants and wildcard permissions
- skill/plugin shell or network behavior
- filesystem reach outside the project
- project vs global scope
- MCP server auto-enablement, authentication, authorization, tool exposure, and destructive capability

Do not assume the client's MCP configuration is the enforcement layer. Verify server/provider authorization where possible.

## Skill execution boundary

`allowed-tools` is **not** a tool restriction in Claude Code. It pre-approves listed tools; it does not remove other tools from the model's available capability set.

Do not treat:

```yaml
allowed-tools: Read, Grep, Glob
```

as evidence that a skill is read-only. Determine the effective boundary from the active Claude Code permission environment.

Where supported by the installed version, inspect:

- deny/disallow rules, including `disallowed-tools` where applicable
- project, user, managed, and session permission settings
- CLI/session permission overrides
- permission-bypass or unrestricted modes
- MCP tool availability and provider-side authorization
- hooks that can mutate state or expand authority
- delegated/subagent capability

Deny/disallow controls can remove named tools from the model and therefore provide real local enforcement, but they are denylist-based and may be incomplete. A newly installed or unlisted mutation-capable tool can remain available. Verify the effective tool set; do not describe a denylist as a universal read-only guarantee.

If mutation-capable tools remain available, classify the skill's `Report only. Change nothing.` boundary as **Assumed, Not Enforced** unless another deterministic mechanism blocks mutation.

If the skill package is installed with Claude-specific restrictions outside the portable `SKILL.md`, verify that those restrictions are actually active for the current session. Do not infer enforcement from configuration text or documentation alone.

## Cross-harness execution

Claude Code can discover skills from locations shared with or originating in other harness ecosystems. If a skill was authored or installed through another harness-compatible location, do not assume that harness's permission semantics transferred with it. Audit the authority Claude Code actually grants it here.
