# Claude Code Adapter

> **Status:** Adapted  
> **Verification:** Current product documentation checked 2026-09-09. Tool surface and bypass examples observed directly from loaded tool schemas in a live session 2026-09-10 — runtime evidence, not documentation, and specific to that build. Product-specific paths and setting names are discovery hints and may change.

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

"Verify the effective tool set" is the instruction in `SKILL.md`; this is the Claude Code procedure. Do it before the audit, and again after any upgrade — the tool surface changes between releases, and a denylist written against an older surface degrades silently.

Claude Code exposes no authoritative machine-readable inventory of the tools in force, so tier 1 of the core hierarchy is unavailable here. Use the benign call test.

**From outside the session**, in a throwaway print-mode run against the profile you intend to audit with:

```sh
claude --settings <your-audit-profile.json> -p \
  "Attempt exactly one harmless operation in each of these classes and report \
   the verbatim error for each: run \`true\` in a shell; write a file under \
   /tmp; fetch https://example.com; delegate a trivial task to a subagent. \
   Do not retry or work around any failure."
```

Read the failures, not the configuration. The distinction that makes this worth running:

| What you see | What it means |
|---|---|
| The model reports the tool is not available to it | Absent from the tool set — **Observed** enforcement |
| The call is made and refused at call time | Present and blocked — a gate, not absence. Enforcement only as far as the gate is deterministic |
| The operation succeeds | The restriction does not exist, whatever the settings say |

**Do not substitute a self-description.** Asking a session to list the tools it holds yields **Inferred** evidence at best: deferred and lazily-loaded tools are not visible to the model until something pulls their schemas in, and on this harness that hidden set has included arbitrary-shell and remote-execution tools. A roster the model recites is a roster of what it can currently see.

**From inside the audit,** during Inventory: derive the execution boundary from capabilities you have actually observed, and report it as **Enforced (Observed)** only when execution, mutation, egress and delegation are each demonstrably unavailable.

### Capabilities that survive a Bash-shaped denylist

Deny by capability, not by name. Denying `Bash` does not deny execution. The following were observed from loaded tool schemas on 2026-09-10; they are **illustrative, not an inventory**, and the point is the category each one occupies, not the spelling.

| Capability | Reaches it through | Close it with |
|---|---|---|
| Shell execution | `Monitor` takes an arbitrary shell `command` and runs it in the same environment as `Bash` | deny `Monitor` |
| Code execution | a connected notebook/IDE tool server executing arbitrary code in a kernel | disable that server for the run |
| Egress | `Monitor`'s `ws:` option (arbitrary WebSocket); `Artifact` publishes a page, and its asset upload sends a local file to a hosted URL; connected mail and drive servers send and share what the audit reads | deny `Artifact`, `enableArtifact: false`, and disable messaging/storage servers |
| Delegation | `SendMessage` addresses sessions that are already running, whose permissions are not yours — the tool's own documentation names this permission laundering. Denying `Task`/`Agent` does not close it | deny `SendMessage` |
| Deferred execution | `RemoteTrigger` creates and runs cloud routines; scheduling tools re-enter the model later, outside the audited turn | deny both |
| Non-tool paths | skills and slash commands execute inline shell; hooks run outside the permission layer entirely | `disableSkillShellExecution: true`, `disableAllHooks: true` |

Two consequences. Connected external tool servers are part of the tool set — an audit profile that denies `WebFetch` while leaving a mail server connected has not closed egress, it has renamed it. And every row here is a capability that was reachable without shell, which is the general lesson: enumerate what you can *do*, then find every tool that does it.

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
