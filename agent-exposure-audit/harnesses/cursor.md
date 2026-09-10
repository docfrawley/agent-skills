# Cursor Adapter

Use this adapter when Cursor Agent or Cursor Cloud Agents are the active harness.

Cursor documents Agent Skills as an open standard and currently discovers skills from native Cursor and neutral `.agents` locations, with compatibility for Claude and Codex skill directories. Treat paths as version-sensitive and verify current behavior when possible.

## Skill discovery

Inspect, where accessible:

- `.agents/skills/`
- `.cursor/skills/`
- `~/.agents/skills/`
- `~/.cursor/skills/`
- compatible Claude/Codex skill locations if present
- nested project skill directories in monorepos
- Cloud Agent skill-sync state when relevant

Determine which skills are actually available to the active local or cloud runtime; local presence does not prove cloud availability.

## Authority checks

Inspect current Cursor settings and workspace/project configuration for:

- filesystem reach
- terminal/shell execution
- network/browser/web authority
- MCP/tool-server configuration
- project/workspace trust
- approval or confirmation behavior
- rules/instructions loaded into Agent
- Cloud Agent vs local-agent differences
- extension/plugin authority
- inherited environment and credentials

Do not assume a UI label such as "ask" or "safe" is a deterministic boundary. Determine which actions it actually gates and whether any tools bypass that path.

## Remote/cloud execution

When Cloud Agents or remote workers are involved, distinguish:

- local machine authority
- remote worker filesystem/network authority
- which skills/configuration were synced or copied
- which credentials are present in the remote environment
- whether repository or workspace secrets are made available

A local audit cannot infer cloud-agent reach solely from local settings.
