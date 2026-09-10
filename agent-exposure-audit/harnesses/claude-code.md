# Claude Code Adapter

> **Status:** Adapted  
> **Verification:** Current product documentation checked 2026-09-09. Tool surface and bypass table verified empirically against a live session 2026-09-10. Product-specific paths and setting names are discovery hints and may change.

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

## Verifying the effective tool set

"Verify the effective tool set" is the instruction above; this is the procedure. Do it before the audit, and again after any Claude Code upgrade — the tool surface changes between releases, and a denylist written against an older surface degrades silently.

**From outside the session** (an operator step, in a throwaway print-mode run):

```sh
claude --settings <your-audit-profile.json> -p \
  "List every tool you actually have available, including deferred ones. \
   Then state whether you have any way to execute a shell command, write a \
   file, reach the network, or delegate to another agent."
```

Read the answer, not the config. Deny rules remove tools from the model's set entirely, so a correctly restricted session reports the tools as **absent**, not as blocked-by-permission. If the run reports a tool as merely "blocked," the restriction is a prompt and not enforcement.

**From inside the audit,** during Inventory: enumerate your own tools and derive the execution boundary from that list alone. Report it as **Enforced (Observed)** only when no shell, write, egress, or delegation tool is present.

### Bypasses a Bash-shaped denylist misses

Denying `Bash` does not deny execution. Verified against Claude Code as of 2026-09-10:

| Surface | Why it survives | Close it with |
|---|---|---|
| `Monitor` | takes an arbitrary shell `command` and runs it in the same environment as `Bash`; its `ws:` option is arbitrary WebSocket egress | deny `Monitor` |
| `Artifact` | publishes a page to the web — egress in publish shape | deny `Artifact`, or `enableArtifact: false` |
| Skills / slash commands | can execute inline shell of their own | `disableSkillShellExecution: true` |
| Hooks | run outside the permission layer entirely; a `PostToolUse` hook shelling out to a formatter is mutation the denylist never sees | `disableAllHooks: true` |
| Background task tools | can read or stop tasks started elsewhere in the session | deny `TaskOutput`/`TaskStop` if a session may already hold running tasks |

This table is evidence of the general point, not a replacement list to trust. Enumerate; do not assume it is current.

## Auditing a credential without copying it

Claude Code writes session transcripts to `~/.claude/projects/<sanitized-cwd>/*.jsonl`, and a tool result containing a file's contents is written there verbatim. Reading a plaintext credential to audit it therefore creates a second durable copy of that credential on disk, in a file the audit's read-only profile does not protect and does not mention.

This is observable: after a run, the audit's own transcript can be searched for a secret the audit reported on, and found.

Consequences for this adapter:

- Prefer `Grep` for a credential's **variable name** over `Read` of the whole file; cite `file:line` from the match.
- Where liveness matters, establish it out-of-band — an operator comparing hashes, or a control-plane query the audit itself does not make — rather than reading the value to compare it.
- Where a value must enter context, treat the transcript as one of the locations that secret now occupies, and name it in the finding's reach.
- Remediation advice should say so: rotating a credential closes every copy at once, including transcripts and backups nobody enumerated. Deleting discovered copies does not.

## Cross-harness execution

Claude Code can discover skills from locations shared with or originating in other harness ecosystems. If a skill was authored or installed through another harness-compatible location, do not assume that harness's permission semantics transferred with it. Audit the authority Claude Code actually grants it here.
