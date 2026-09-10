# Harness Adapters

Read this index after inventory identifies the active coding-agent or agent-runtime environment.

## Purpose

The core audit is harness-agnostic. Adapters provide **discovery hints and product-specific enforcement checks**, not a different security model.

The invariant questions are always:

- What instructions and skills are loaded?
- What tools can the harness invoke?
- What filesystem can it reach?
- Can it execute shell/terminal commands?
- Can it reach the network or browser?
- What MCP/tool servers are enabled?
- What hooks or automatic execution paths exist?
- Can it delegate to other agents/processes?
- What credentials and ambient identities does it inherit?
- What approval/sandbox mechanism is supposed to constrain it?
- What deterministic mechanism actually enforces those constraints?

## Available adapters

- `claude-code.md`
- `codex.md`
- `cursor.md`
- `pi.md`

Read only the adapter(s) relevant to the environment actually discovered.

## Unknown harnesses

If no adapter exists:

1. Do not substitute another harness's configuration names.
2. Inspect the generic capabilities above using available files, runtime metadata, help output, or authoritative local/current documentation.
3. Classify product-specific controls as **Unverified** where they cannot be established.
4. Continue the application-side audit normally.


## Cross-harness authority

A portable skill can be discovered or executed by a different harness than the one it was authored for. Treat that transition as an authority boundary.

Ask:

> **What authority did this skill acquire when it crossed harnesses?**

When a skill is loaded from another harness's directory, compatibility path, plugin bundle, or shared `.agents/skills/` location:

- do not assume the originating harness's allow/deny rules transfer
- do not assume the same filesystem, shell, network, MCP, approval, hook, or delegation semantics apply
- determine the effective capability set in the **current** harness
- classify unavailable permission semantics as **Unverified** rather than borrowing another adapter's model

## Version drift

Every adapter is a set of examples, not a permanent schema. Harnesses change quickly.

- Verify key names, hook names, discovery locations, approval modes, and precedence against the installed version or current authoritative documentation where possible.
- Failure to find an example key is **not itself a finding**.
- Audit the underlying capability rather than the spelling of a setting.

**Policy is portable. Capability is inherited. Enforcement is local.**
