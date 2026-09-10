# OpenCode adapter

> **Status:** Adapted  
> **Verification:** Current product documentation checked 2026-09-10. Product-specific paths and setting names are discovery hints and may change.

Read this adapter when OpenCode is the active harness.

Current OpenCode documentation describes native Agent Skills discovery from:

- project `.opencode/skills/`
- global `~/.config/opencode/skills/`
- compatibility `.claude/skills/`
- neutral `.agents/skills/`
- corresponding global compatibility locations

OpenCode can also configure additional skill sources in `opencode.json` / `opencode.jsonc`.

## Audit

Inspect, where present:

- `opencode.json` / `opencode.jsonc`
- native and compatibility skill directories
- configured additional skill sources, including remote catalogs
- agent-specific permission overrides
- shell/command authority
- filesystem scope
- network reach
- MCP or external tool configuration
- environment and credential inheritance

OpenCode documents skill permissions and per-agent overrides. Determine the **effective** permissions rather than treating the presence of a skill as evidence of its authority.

Unknown frontmatter fields may be ignored by OpenCode. Do not assume a tool-control field from another harness transfers merely because the skill loads.

## Cross-harness check

Because OpenCode can discover `.claude/skills/` and `.agents/skills/`, explicitly ask:

> What authority did this skill acquire when OpenCode loaded it?

Do not inherit Claude Code permission assumptions from a Claude-compatible path.

Treat paths and setting names as discovery hints and verify against the installed/current OpenCode version when possible.
