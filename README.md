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

Not a diff review — use the built-in `security-review` for pending branch
changes.

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
