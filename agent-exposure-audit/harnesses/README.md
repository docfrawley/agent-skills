# Harness adapters

Harness adapters provide product-specific discovery hints for the portable Agent Exposure Audit.

They are **not** a support boundary.

> **An adapter improves enumeration. It does not define the methodology.**

The core audit remains:

**Inventory → Identity → Reach → Boundary → Enforcement → Ownership**

and applies even when no adapter exists.

## Compatibility model

Use these terms precisely:

| Level | Meaning |
|---|---|
| **Portable** | The core audit can run without harness-specific assumptions. |
| **Adapted** | This package includes product-specific discovery and enforcement guidance for the harness. |
| **Native Agent Skills** | Current authoritative documentation confirms the harness can discover/load `SKILL.md` Agent Skills. |
| **Generic fallback** | No verified adapter or native skill path is available; audit effective capabilities directly. |
| **Translatable** | The methodology applies, but the environment may require its own rules/instructions format rather than native Agent Skills loading. |

## Adapter registry

| Harness | Adapter | Status | Verified |
|---|---|---|---|
| Claude Code | `claude-code.md` | Adapted | 2026-09-09 |
| Codex | `codex.md` | Adapted | 2026-09-09 |
| Cursor | `cursor.md` | Adapted | 2026-09-09 |
| Pi | `pi.md` | Adapted | 2026-09-09 |
| OpenCode | `opencode.md` | Adapted | 2026-09-10 |
| GitHub Copilot | `github-copilot.md` | Adapted | 2026-09-10 |
| Gemini CLI | `gemini-cli.md` | Adapted | 2026-09-10 |
| Windsurf | `windsurf.md` | Provisional | Unverified |

A verification date means the adapter was checked against current product documentation on that date. It is evidence of review, not a freshness guarantee.

This registry is intentionally not exhaustive. New harnesses use the core methodology until a tested adapter is contributed.

## Generic fallback

If the active harness has no adapter, **continue the audit using the portable invariants and workflow in `SKILL.md`**.

Do not duplicate or invent product-specific schemas. Record product-specific controls as **Observed**, **Inferred**, or **Unverified** using the normal evidence model.

## Cross-harness authority

Portable instructions do not imply portable permissions.

Whenever a skill can be discovered from a neutral directory such as `.agents/skills/`, from another harness's compatibility directory, or through an import/link mechanism, ask:

> **What authority did this skill acquire when it crossed harnesses?**

Compare, where evidence permits:

- source harness or expected capability model
- current harness
- filesystem scope
- shell authority
- network authority
- MCP/tool access
- automatic execution
- delegation
- approval requirements
- inherited credentials

A skill that was read-only in one runtime may not be read-only in another.

## Version drift

Paths, setting names, hook names, and permission semantics change.

Adapter-specific names are discovery hints, not permanent schema. Verify them against the installed version or current authoritative documentation when possible. Failure to find an example key is not itself a finding; audit the underlying capability.
