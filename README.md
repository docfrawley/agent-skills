# claude-skills

Agent skills for [Claude Code](https://claude.com/claude-code). One directory
per skill, each holding a `SKILL.md` and whatever references it loads.

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

It is harness-agnostic. The method is portable; per-harness discovery hints
live in [`harnesses/`](./agent-exposure-audit/harnesses) for Claude Code,
Codex, Cursor and Pi, and an unknown harness falls back to the generic
capability model rather than borrowing another's schema.

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
own permission settings. On Claude Code, deny rules and `disallowed-tools` can
remove named tools from the model. Consider denying at least `Write`, `Edit`,
`NotebookEdit`, `Bash`, `WebFetch`, `WebSearch` and `Task` for the run — the
last three because an auditor that reads credentials must not also be able to
send what it reads or delegate around its own limits.

Note the limit: those controls are **denylists**, so a mutation-capable tool
installed later, or simply not named, remains available. Verify the effective
tool set rather than trusting the list. Checks that genuinely need shell —
repository history for secrets, file ownership, symlink resolution — are
reported under **Could Not Enumerate** naming the narrowest capability that
would close each gap.

## Install

Clone anywhere, then symlink each skill you want into `~/.claude/skills/`:

```sh
git clone https://github.com/docfrawley/claude-skills.git ~/projects/claude-skills
ln -s ~/projects/claude-skills/agent-exposure-audit ~/.claude/skills/agent-exposure-audit
```

Symlinking rather than copying keeps the repo the single source of truth —
editing a skill and committing it are the same act, so the installed copy
cannot drift.

Skills in `~/.claude/skills/` are available in every project. For a skill that
should only apply to one repo, symlink it into that repo's `.claude/skills/`
instead.

## License

[MIT](./LICENSE).
