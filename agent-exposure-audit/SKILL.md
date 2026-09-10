---
name: agent-exposure-audit
description: "Audits AI and agent exposure across a codebase and, when accessible, the local agent-harness environment. Inventories AI frameworks, agent runtimes, MCP servers, credentials, ambient identities, hooks, skills, and plugins; maps downstream reach; and identifies boundaries that are asserted but not enforced. Use for AI/agent security audits, MCP exposure, credential scoping, agent permissions, egress, blast radius, shadow AI infrastructure, prompt-injection reach, harness security, or questions about what an agent or framework can reach. Not a diff review."
---

# Agent Exposure Audit

An on-demand audit of AI and agent exposure across a repository and, when accessible, the local agent environment.

**Report only. Change nothing.**

## Core methodology

Use this sequence:

**Inventory → Identity → Reach → Boundary → Enforcement → Ownership**

Three principles govern every audit:

1. **Inventory before security.** You cannot secure an agent or framework you do not know exists.
2. **Reach before severity.** Rank risk by what becomes reachable if a component is compromised, manipulated, or behaves unexpectedly, not CVSS alone.
3. **Enforcement before intent.** Configuration, prompts, comments, policy text, and names describe intended boundaries. Determine what actually enforces them.

The dangerous exposure is often not in the latest diff. It is the framework installed months ago, the credential added to make a pilot work, the globally available tool server, an inherited cloud role, or a boundary described in configuration but never enforced by infrastructure.

## Evidence model

Classify every reach or control claim as:

- **Observed** — directly demonstrated by available code, configuration, or runtime evidence.
- **Inferred** — strongly implied by available evidence but not independently verified.
- **Unverified** — determining effective reach or enforcement requires access to an unavailable external system, control plane, or harness capability.

Never present inferred access as observed access. Never convert configuration intent into a claim about effective permissions.

## Required resources

Verify these core files are available before beginning:

- `references/inventory.md`
- `references/credentials-and-identity.md`
- `references/boundaries-and-egress.md`
- `references/platform-checks.md`
- `references/prototype-drift.md`
- `references/reporting.md`
- `harnesses/README.md`

If a required resource is missing or inaccessible, record it under **Could Not Enumerate** and state which part of the audit is affected.

Harness adapters under `harnesses/` are conditional. Use the matching adapter when the active harness is identified. If no adapter exists, continue with the generic capability model rather than assuming another harness's schema.

## Execution boundary

The audit's own behavior must follow the same **enforcement before intent** principle it applies to the target system.

**Policy is portable. Capability is inherited. Enforcement is local.**

Before inspecting the target, determine the active harness's effective tool and permission surface where possible. Prefer an execution environment that deterministically blocks mutation and unnecessary egress.

- Do not broaden permissions to make the audit easier.
- Do not use unrestricted or permission-bypass modes for this audit.
- Treat unrestricted shell, write-capable tools, broad filesystem access, outbound network tools, mutation-capable hooks, or delegated agents as authority the audit could theoretically exercise even though this skill says not to use it.
- If the environment does not enforce a read-only boundary, continue only with passive operations and record the audit's own mutation/egress boundary as **Assumed, Not Enforced** or **Unverified**, as appropriate.
- If a necessary inspection cannot be completed without gaining broader authority, put it under **Could Not Enumerate** rather than silently escalating.

Do not assume a tool policy exists merely because this file says "change nothing." Verify the effective permission layer where possible.

## Scope

Audit both surfaces when accessible:

1. **Application surface** — repository, dependencies, runtime manifests, infrastructure configuration, and production paths.
2. **Agent-harness surface** — active coding/agent harness, MCP/tool servers, hooks, skills, agents/subagents, plugins/extensions, approval modes, filesystem reach, network reach, and user/global configuration available to the runtime.

Never silently skip a surface. If a surface cannot be inspected, record it under **Could Not Enumerate**.

For a large repository or monorepo, state scope before scanning:

- what will be inspected exhaustively
- what, if anything, will be sampled
- which machine-level or external surfaces are accessible
- known enumeration limitations

Do not imply exhaustive coverage where sampling was used.

## Audit workflow

### 1. Inventory first

**Read `references/inventory.md` before beginning this step.** It contains dependency, runtime, harness, MCP/tool-server, hook, skill, plugin, and deployment-surface enumeration. These checks are not reproduced here.

Identify the active harness if possible. Then **read `harnesses/README.md` and the matching harness adapter** when one exists.

Produce the inventory before beginning risk analysis. Incomplete inventory is itself a result.

### 2. Load relevant platform checks

**Read `references/platform-checks.md` after inventory.** Read only sections relevant to technologies actually discovered.

These checks feed both identity/reach analysis and boundary/enforcement analysis.

### 3. Map identity and reach

**Read `references/credentials-and-identity.md` before beginning this step.** It contains the ambient-authority checklist, secret-history classification, and per-service scope questions. These checks are not reproduced here.

For every component determine:

- What identity does it run as?
- What explicit credentials does it receive?
- What ambient authority does it inherit?
- What systems, data, APIs, and actions become reachable?

### 4. Test claimed boundaries and hostile-input reach

**Read `references/boundaries-and-egress.md` before beginning this step.** It contains asserted-boundary search, tool-permission checks, sandbox verification, egress analysis, prompt-injection reach, HTTP assumption checks, and dynamic-execution checks.

Find boundaries described as read-only, sandboxed, internal, restricted, safe, isolated, private, allowlisted, approved, or similar. Identify the deterministic mechanism enforcing each claim.

Also identify paths where **untrusted content + sensitive reach + outbound/action capability** coexist, even when no code execution is involved.

If nothing enforces a claimed boundary, that is a finding. If enforcement cannot be verified, report it as **Unverified**.

### 5. Check prototype and ownership drift

**Read `references/prototype-drift.md` before beginning this step.** It contains prototype-to-production drift and ownership checks.

Look for experiments that quietly became infrastructure, long-lived pilot credentials, abandoned components, and live systems with unclear ownership.

### 6. Build the blast-radius map

For every significant component report:

| Component | Exposure | Identity / Credential | Downstream Reach | Boundary | Evidence | Priority |
|---|---|---|---|---|---|---|

The map must answer:

> **If this component is compromised, manipulated, or behaves unexpectedly, what becomes reachable next?**

Do not include secret values.

### 7. Report

**Read `references/reporting.md` before producing the final report.** It contains ranking rules, finding structure, output categories, enumeration-gap requirements, and report-scaling guidance.

Rank findings by downstream blast radius. Lead with the widest-reach finding.

## Rules

- **Never expose secrets.** Never print, echo, partially reveal, or summarize secret values. Reference credential name, source, consuming component, and `file:line` only.
- **Change nothing.** Do not edit files, rotate credentials, revoke tokens, modify IAM, alter tool-server configuration, install packages, change network rules, commit code, or open pull requests.
- **Prefer passive inspection.** Use structurally read-only capabilities whenever possible.
- **Do not prove reach destructively.** Do not test credentials with state-changing requests or probe production merely to demonstrate access.
- **Inventory before conclusions.** Establish what exists before beginning vulnerability analysis.
- **Prefer reach over severity.** Downstream authority matters more than a severity label alone.
- **Distinguish intent from enforcement.** A prompt, comment, variable name, approval description, or application setting is not a deterministic security boundary.
- **State uncertainty precisely.** An unverified control is neither absent nor present until evidence establishes which.
- **Absence of evidence is a result, not proof of absence.** Say "No egress restriction found in the available configuration," not "No egress restriction exists."
- **Do not overclaim completeness.** A repo or local-machine audit cannot prove organization-wide absence of shadow AI infrastructure.
- **Do not assume harness equivalence.** Tool names, config keys, hook events, permission semantics, and skill locations vary by harness and version.
- **Do not assume permission portability.** When a skill, plugin, or instruction package is discovered from another harness's directory or compatibility path, determine what authority it inherits in the current harness. The originating harness's restrictions do not automatically transfer.

## Audit heuristic

When uncertain where to spend investigation time, ask in this order:

1. What is this?
2. What identity does it run as?
3. What can that identity reach?
4. What untrusted input can influence it?
5. If this skill, agent, or plugin crossed harnesses, what authority did it acquire here?
6. What is it supposed to be prevented from doing?
7. What deterministic mechanism enforces that boundary?
8. Who owns the component and credential?

The audit is complete when these questions are answered where evidence permits and explicitly marked **Unverified** where they do not.
