# Gemini CLI adapter

> **Status:** Adapted  
> **Verification:** Current product documentation checked 2026-09-10. Product-specific paths and setting names are discovery hints and may change.

Read this adapter when Gemini CLI is the active harness.

Current Gemini CLI documentation describes Agent Skills as based on the open Agent Skills standard.

Documented discovery includes:

- user `~/.gemini/skills/`
- user `~/.agents/skills/`
- workspace `.gemini/skills/`
- workspace `.agents/skills/`
- built-in skills
- skills bundled in extensions

Gemini CLI documents activation consent before a skill gains access to its bundled resources.

## Audit

Inspect, where applicable:

- `.gemini/skills/` and `.agents/skills/`
- `~/.gemini/skills/` and `~/.agents/skills/`
- installed extensions and extension-provided skills
- `GEMINI.md` and other persistent instruction sources
- workspace trust state
- shell/command execution authority
- filesystem scope beyond the skill directory
- network reach
- MCP/external tool configuration
- inherited environment and credentials
- approval/consent behavior
- delegation or subagent capabilities

Do not confuse activation consent with containment of everything the active runtime can subsequently reach.

## Cross-harness check

Because `.agents/skills/` is a neutral alias, explicitly ask what authority a portable skill receives after Gemini CLI activates it.

A skill's originating harness restrictions do not automatically transfer.

Treat paths and setting names as discovery hints and verify against the installed/current Gemini CLI version when possible.
