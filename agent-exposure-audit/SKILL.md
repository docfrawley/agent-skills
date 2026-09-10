---
name: agent-exposure-audit
description: "Audits AI and agent exposure across a codebase and, when accessible, the local Claude Code and MCP environment. Inventories AI frameworks, agent runtimes, MCP servers, credentials, and ambient identities; maps downstream reach; and identifies boundaries that are asserted but not enforced. Use for AI/agent security audits, MCP exposure, credential scoping, agent permissions, egress, blast radius, shadow AI infrastructure, prompt-injection reach, and questions about what an agent or framework can reach. Not a diff review — use security-review for pending branch changes."
allowed-tools:
  - Read
  - Grep
  - Glob
---

# Agent Exposure Audit

An on-demand audit of AI and agent exposure across a repository and, when accessible, the local agent environment.

**Report only. Change nothing.**

## Core methodology

Use this sequence:

**Inventory → Identity → Reach → Boundary → Enforcement → Ownership**

Three principles govern every audit:

1. **Inventory before security.** You cannot secure an agent or framework you do not know exists.
2. **Reach before severity.** Rank risk by what becomes reachable if a component is compromised or behaves unexpectedly, not CVSS alone.
3. **Enforcement before intent.** Configuration, prompts, comments, and names describe intended boundaries. Determine what actually enforces them.

The dangerous exposure is often not in the latest diff. It is the framework installed months ago, the credential added to make a pilot work, the globally available MCP server, an inherited cloud role, or a boundary described in configuration but never enforced by infrastructure.

## Evidence model

Classify every reach or control claim as:

- **Observed** — directly demonstrated by available code, configuration, or runtime evidence.
- **Inferred** — strongly implied by available evidence but not independently verified.
- **Unverified** — determining effective reach or enforcement requires access to an unavailable external system or control plane.

Never present inferred access as observed access. Never convert configuration intent into a claim about effective permissions.

## Required references

This skill depends on these files:

- `references/inventory.md`
- `references/credentials-and-identity.md`
- `references/boundaries-and-egress.md`
- `references/platform-checks.md`
- `references/prototype-drift.md`
- `references/reporting.md`

Before beginning an audit, verify that all six are available.

If a required reference is missing or inaccessible, do not silently continue as though the full procedure was performed. Record the missing reference under **Could Not Enumerate** and state which part of the audit is affected.

## Execution boundary

The audit's own behavior must follow the same **enforcement before intent** principle it applies to the target system.

Before inspecting the target, determine the effective Claude Code/tool-permission surface when it is available to inspect. Prefer an execution environment that deterministically blocks mutation: no Edit/Write authority, no state-changing MCP tools, and Bash constrained to specific read-only inspection commands rather than unrestricted shell access.

- Do not broaden permissions to make the audit easier.
- Do not use `bypassPermissions` or equivalent unrestricted modes for this audit.
- Treat unrestricted Bash, write-capable MCP tools, broad filesystem access, or mutation-capable hooks as authority the audit could theoretically exercise even though this skill instructs the model not to use it.
- If the environment does not enforce a read-only boundary, continue only with passive operations and record the audit's own mutation boundary as **Assumed, Not Enforced** or **Unverified**, as appropriate.
- If a necessary inspection cannot be completed without gaining broader authority, put it under **Could Not Enumerate** rather than requesting or exercising unnecessary write capability.

Do not assume a tool policy exists merely because this file says "change nothing." Verify the effective permission layer where possible.

### This skill's own boundary

This skill declares `allowed-tools: Read, Grep, Glob` in its frontmatter, which Claude Code enforces at the tool layer while the skill is active. That is a deterministic control rather than an instruction, so it satisfies the standard this section applies to everything else.

Three exclusions are deliberate:

- **No `Bash`.** Unrestricted shell is mutation-capable, so listing it would produce exactly the *looks enforced but is not* boundary this audit exists to find. Checks that genuinely require shell — repository history for secrets, file ownership and mode, resolving symlinks, control-plane queries — are therefore unavailable by default. Record them under **Could Not Enumerate** with the capability needed, and let the operator decide whether to grant it deliberately.
- **No network tools.** An auditor reads credentials, configuration, and other sensitive material. Give that same context an outbound path and it becomes the pattern this skill warns about: untrusted content plus sensitive reach plus egress. The audit must not be able to send what it reads.
- **No subagent delegation.** Work handed to another agent may not inherit this tool restriction, which would route around the boundary rather than respect it.

An operator who wants the deeper checks should grant the capability explicitly for that run, not weaken the default that ships to everyone.

## Scope

Audit both surfaces when accessible:

1. **Application surface** — repository, dependencies, runtime manifests, infrastructure configuration, and production paths.
2. **Local agent surface** — Claude Code, MCP servers, hooks, skills, agents, plugins, and user-level configuration available on the machine.

Never silently skip a surface. If a surface cannot be inspected, record it under **Could Not Enumerate**.

For a large repository or monorepo, state scope before scanning:

- what will be inspected exhaustively
- what, if anything, will be sampled
- which machine-level or external surfaces are accessible
- known enumeration limitations

Do not imply exhaustive coverage where sampling was used.

## Audit workflow

### 1. Inventory first

**Read `references/inventory.md` before beginning this step.** It contains the dependency, runtime, Claude Code, MCP, hook, skill, plugin, and deployment-surface enumeration procedure. These checks are not reproduced here.

Inventory AI frameworks, agent runtimes, MCP servers, hooks, skills, plugins, and runtime infrastructure before beginning risk analysis.

Produce the inventory table first. Incomplete inventory is itself a result.

### 2. Load relevant platform checks

**Read `references/platform-checks.md` after inventory.** Read only sections relevant to technologies discovered during inventory.

These checks feed both identity/reach analysis and boundary/enforcement analysis. Do not defer platform-specific checks until the boundary phase.

### 3. Map identity and reach

**Read `references/credentials-and-identity.md` before beginning this step.** It contains the ambient-authority checklist, secret-history classification, and per-service scope questions. These checks are not reproduced here.

For every component determine:

- What identity does it run as?
- What explicit credentials does it receive?
- What ambient authority does it inherit?
- What systems, data, APIs, and actions become reachable?

### 4. Test claimed boundaries and hostile-input reach

**Read `references/boundaries-and-egress.md` before beginning this step.** It contains asserted-boundary search, tool-permission checks, sandbox verification, egress analysis, prompt-injection reach, HTTP assumption checks, and dynamic-execution checks. These checks are not reproduced here.

Find boundaries described as read-only, sandboxed, internal, restricted, safe, isolated, private, allowlisted, or similar. Identify the deterministic mechanism enforcing each claim.

Also identify paths where **untrusted content + sensitive reach + outbound/action capability** coexist, even when no code execution is involved.

If nothing enforces a claimed boundary, that is a finding. If enforcement cannot be verified, report it as **Unverified** rather than treating it as present or absent.

### 5. Check prototype and ownership drift

**Read `references/prototype-drift.md` before beginning this step.** It contains the prototype-to-production drift and ownership checks. These checks are not reproduced here.

Look for experiments that quietly became infrastructure, long-lived pilot credentials, abandoned components, and live systems with unclear ownership.

### 6. Build the blast-radius map

For every significant component report:

| Component | Exposure | Identity / Credential | Downstream Reach | Boundary | Evidence | Priority |
|---|---|---|---|---|---|---|

The map must answer:

> **If this component is compromised, manipulated, or behaves unexpectedly, what becomes reachable next?**

Do not include secret values.

### 7. Report

**Read `references/reporting.md` before producing the final report.** It contains ranking rules, finding structure, output categories, enumeration-gap requirements, and report-scaling guidance. These requirements are not reproduced here.

Rank findings by downstream blast radius. Lead with the widest-reach finding.

## Rules

- **Never expose secrets.** Never print, echo, partially reveal, or summarize secret values. Reference credential name, source, consuming component, and `file:line` only.
- **Change nothing.** Do not edit files, rotate credentials, revoke tokens, modify IAM, alter MCP configuration, install packages, change network rules, commit code, or open pull requests.
- **Prefer passive inspection.** Use read-only commands and static analysis whenever possible.
- **Do not prove reach destructively.** Do not test credentials with state-changing requests or probe production merely to demonstrate access.
- **Inventory before conclusions.** Establish what exists before beginning vulnerability analysis.
- **Prefer reach over severity.** Downstream authority matters more than a severity label alone.
- **Distinguish intent from enforcement.** A prompt, comment, variable name, or application setting is not a deterministic security boundary.
- **State uncertainty precisely.** An unverified control is neither absent nor present until evidence establishes which.
- **Absence of evidence is a result, not proof of absence.** Say “No egress restriction found in the available configuration,” not “No egress restriction exists.”
- **Do not overclaim completeness.** A repo or local-machine audit cannot prove organization-wide absence of shadow AI infrastructure.

## Audit heuristic

When uncertain where to spend investigation time, ask in this order:

1. What is this?
2. What identity does it run as?
3. What can that identity reach?
4. What untrusted input can influence it?
5. What is it supposed to be prevented from doing?
6. What deterministic mechanism enforces that boundary?
7. Who owns the component and credential?

The audit is complete when these questions are answered where evidence permits and explicitly marked **Unverified** where they do not.
