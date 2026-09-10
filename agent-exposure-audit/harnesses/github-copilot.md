# GitHub Copilot adapter

> **Status:** Adapted  
> **Verification:** Current product documentation checked 2026-09-10. Product-specific paths and setting names are discovery hints and may change.

Read this adapter when GitHub Copilot is the active harness.

Current GitHub documentation describes Agent Skills across Copilot cloud agent, code review, Copilot CLI, the Copilot app, and agent mode in supported IDEs.

Documented skill locations include:

- project `.github/skills/`
- project `.claude/skills/`
- project `.agents/skills/`
- personal `~/.copilot/skills/`
- personal `~/.agents/skills/`

## Audit

Determine which Copilot surface is active. Cloud agent, code review, CLI, and IDE agent mode can have materially different authority.

Inspect, where applicable:

- skill discovery paths
- repository custom instructions and custom agents
- `.github/hooks/*.json`
- MCP configuration and repository MCP settings
- shell or terminal authority
- filesystem/workspace scope
- network reach
- repository write authority
- workflow modification authority
- secrets and environment exposure
- subagent/delegation capability
- approval/confirmation behavior

GitHub documents `allowed-tools` as pre-approval behavior for skills. Do not treat pre-approval as a read-only restriction.

Pay special attention to whether the agent can modify `.github/workflows/` or other deployment paths. Write access there can inherit CI/CD authority and secrets downstream.

## Cross-harness check

Copilot can discover skills from `.claude/skills/` and `.agents/skills/`.

Do not assume the originating harness's permission model transfers. Audit the effective Copilot capability set in the current surface.

Treat paths and setting names as discovery hints and verify against current GitHub documentation when possible.
