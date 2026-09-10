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
verification — Windsurf. Each adapter carries the date its product
documentation was checked. An unknown harness falls back to the invariants
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
own permission settings. On Claude Code, deny rules and `disallowed-tools`
remove named tools from the model's set entirely, so they are real local
enforcement rather than a prompt.

Deny at minimum `Write`, `Edit`, `NotebookEdit`, `Bash`, `WebFetch`,
`WebSearch` and `Task`/`Agent` — the last three because an auditor that reads
credentials must not also be able to send what it reads or delegate around its
own limits.

**Treat that as a floor, not a set.** These controls are **denylists**, so a
mutation-capable tool that is newer than this file, or simply unnamed, remains
available — and the tool surface moves faster than the list. Two that a
Bash-shaped denylist misses today:

- **`Monitor`** takes an arbitrary shell `command` and runs it in the same
  environment as `Bash`, plus a `ws:` option for arbitrary WebSocket egress. It
  survives a `Bash` deny completely.
- **`Artifact`** publishes a page to the web. An auditor that reads credentials
  must not hold a publish button.

Shell also re-enters through paths that are not tools: skills and slash
commands can execute inline shell, and hooks run outside the permission layer
altogether. On Claude Code, `disableSkillShellExecution` and `disableAllHooks`
close those.

**So do not trust any list, including this one — verify.** The adapter carries
the one-command procedure: [Verifying the effective tool
set](./agent-exposure-audit/harnesses/claude-code.md#verifying-the-effective-tool-set).
Run it before the audit, and again after any harness upgrade.

Checks that genuinely need shell — repository history for secrets, file
ownership, symlink resolution — are reported under **Could Not Enumerate**
naming the narrowest capability that would close each gap.

### Auditing a credential creates a copy of it

The audit's no-secrets rule governs its **report**, and the report obeys it:
findings cite a credential's name, source, consumer and `file:line`, never a
value. But persistence happens at **input**, not output. Reading a file to
audit it puts the contents in the model's context, and most harnesses write
that context to a session transcript on disk — so auditing a plaintext
credential silently creates a durable second copy of it, outside whatever
protections the original had.

No read-only profile prevents this: the harness writes the transcript, not the
model. Prefer identifying credentials by name and location over reading their
values, establish liveness out-of-band where you can, and treat the audit's own
transcript as in-scope when reporting where a secret now lives.

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
