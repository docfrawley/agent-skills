# agent-skills

Skills in the [Agent Skills](https://code.claude.com/docs/en/skills) format —
one directory per skill, each holding a `SKILL.md` and whatever references it
loads. The format is supported across harnesses, including Claude Code, Codex,
Cursor, Pi, OpenCode, GitHub Copilot and Gemini CLI.

## Skills

### [`agent-exposure-audit`](./agent-exposure-audit)

An on-demand audit of AI and agent exposure across a repository and, where
accessible, the local agent environment — MCP servers, hooks, skills, plugins,
credentials, and the identities they inherit.

It follows one sequence — **Inventory → Identity → Reach → Boundary →
Enforcement → Ownership** — on three principles:

- **Inventory before security.** You cannot secure what you do not know exists.
- **Reach before severity.** Rank by what becomes reachable if a component is
  compromised, not by CVSS alone.
- **Enforcement before intent.** Config, prompts, and names describe intended
  boundaries. Find what actually enforces them.

Every claim is classified **Observed**, **Inferred**, or **Unverified**, and
findings group as Reachable Now / Over-Scoped / Assumed, Not Enforced /
Unowned / Could Not Enumerate. It reports and changes nothing.

It is harness-agnostic. One set of universal capability invariants is the
method; per-harness discovery hints live in
[`harnesses/`](./agent-exposure-audit/harnesses) for Claude Code, Codex,
Cursor, Pi, OpenCode, GitHub Copilot, Gemini CLI and — provisionally, pending
verification — Windsurf. Each adapter records what its claims rest on —
documentation, or behavior observed in a live session — and when that was
last checked. An unknown harness falls back to the invariants
rather than borrowing another's schema: an adapter tells you where to look,
the invariants tell you what you are looking for.

**Policy is portable. Capability is inherited. Enforcement is local.**

That applies to the skill itself. Its `Report only. Change nothing.` rule is
behavioral, and the skill says so: where mutation-capable tools remain
available it classifies its own boundary as **Assumed, Not Enforced**, exactly
as it would for anything else it audits.

Not a diff review.

### Enforcing the read-only boundary

The skill deliberately ships no tool-restriction frontmatter, because there is
no portable one. In particular `allowed-tools` **pre-approves** listed tools in
Claude Code — it does not remove the others — so declaring it would create the
very *looks enforced but is not* boundary this audit exists to find.

If you want the boundary actually enforced, that is an operator step in your
own permission settings, and the two Claude Code mechanisms are not
equivalent. `disallowedTools` removes a tool from the model's set — it is not
there to call. A permission **deny rule** blocks the call while the tool
remains in the set. Both are useful; only the first is absence. Tell them apart
before recording either as enforcement, because "absent," "present but
refused," and "discouraged in a prompt" are three different boundaries and this
audit exists to distinguish them.

Deny at minimum `Write`, `Edit`, `NotebookEdit`, `Bash`, `WebFetch`,
`WebSearch` and `Task`/`Agent` — the last three because an auditor that reads
credentials must not also be able to send what it reads or delegate around its
own limits.

**Treat that as a floor, not a set, and do not secure by tool name.** These
controls are denylists, so a capability that is newer than this file, or simply
unnamed, remains available — and the tool surface moves faster than any list.
Shell survives a `Bash` deny through other tools, through connected external
tool servers, through other sessions, and through paths that are not tools at
all. Enumerate by **execution, mutation, egress, delegation and credential
use** instead, and verify rather than trust: the adapter carries worked
examples and the procedure — [Verifying the effective tool
set](./agent-exposure-audit/harnesses/claude-code.md#verifying-the-effective-tool-set).
Run it before the audit, and again after any harness upgrade.

Checks that genuinely need shell — repository history for secrets, file
ownership, symlink resolution — are reported under **Could Not Enumerate**
naming the narrowest capability that would close each gap.

### Auditing a credential can copy it

The audit's no-secrets rule governs its **report**, and the report obeys it:
findings cite a credential's name, source, consumer and `file:line`, never a
value. But persistence happens at **input**, not output. A secret read into
context may be written to transcripts, logs, caches or telemetry, creating a
durable copy outside whatever protections the original had. No read-only
profile prevents it — the harness does the writing, not the model.

So the audit can enlarge the set of places a secret exists merely by looking at
it. The skill carries the rule; each adapter carries what its harness actually
persists, which is the part you have to know before deciding whether to read a
value at all.

## Install

Clone anywhere, then symlink each skill you want into the location your
harness reads:

```sh
git clone https://github.com/docfrawley/agent-skills.git ~/projects/agent-skills

# Claude Code
ln -s ~/projects/agent-skills/agent-exposure-audit ~/.claude/skills/agent-exposure-audit

# Neutral location, read by Cursor, Pi and others
ln -s ~/projects/agent-skills/agent-exposure-audit ~/.agents/skills/agent-exposure-audit
```

Symlinking rather than copying keeps the repo the single source of truth —
editing a skill and committing it are the same act, so the installed copy
cannot drift.

A skill in a user-level directory is available in every project. To scope one
to a single repo, symlink it into that repo's skills directory instead.

**Installing a skill does not carry a permission model with it.** Whatever
sandbox, approval mode, filesystem scope or tool policy applied where a skill
was authored, it inherits the authority of the harness running it. That is the
audit's own subject, and it applies to the audit.

## License

[MIT](./LICENSE).
