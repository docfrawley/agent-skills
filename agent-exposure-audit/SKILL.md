---
name: agent-exposure-audit
description: "Audits AI and agent exposure across the local agent-harness environment and, where applicable, the codebase it works in. Inventories AI frameworks, agent runtimes, MCP servers, credentials, ambient identities, hooks, skills, and plugins; maps downstream reach; and identifies boundaries that are asserted but not enforced. Use for AI/agent security audits, MCP exposure, credential scoping, agent permissions, egress, blast radius, shadow AI infrastructure, prompt-injection reach, harness security, or questions about what an agent or framework can reach. Not a diff review."
---

# Agent Exposure Audit

An on-demand audit of AI and agent exposure across the local agent environment and the repository it works in. Both surfaces matter; do not assume the repository is where the exposure is.

**Report only. Change nothing.**

## Method

**Inventory → Identity → Reach → Boundary → Enforcement → Ownership**

1. **Inventory before security.** You cannot secure an agent or framework you do not know exists.
2. **Reach before severity.** Rank by what becomes reachable if a component is compromised, manipulated, or misbehaves — not CVSS alone.
3. **Enforcement before intent.** Configuration, prompts, comments, policy text, and names describe intended boundaries. Find what actually enforces them.

The dangerous exposure is often not in the latest diff: the framework installed months ago, the pilot credential, the globally available tool server, the inherited cloud role, the boundary described but never enforced.

For every harness, whatever its schema, determine: what instructions are loaded; what filesystem, execution, and network it has; what tools or MCP servers it can invoke; what credentials or identities it inherits; what runs automatically; what it can delegate; what requires approval; and **what actually enforces each of those**.

Adapters tell you where to look. These questions tell you what you are looking for. **Policy is portable. Capability is inherited. Enforcement is local.**

## Evidence

Classify every reach or control claim:

- **Observed** — directly demonstrated by code, configuration, or runtime evidence.
- **Inferred** — strongly implied but not independently verified.
- **Unverified** — answering it needs an external system, control plane, or capability you do not have.

Never present inferred access as observed. Never convert configuration intent into a claim about effective permissions. An unverified control is neither present nor absent. Absence of evidence is a result, not proof of absence: say "no egress restriction found in the available configuration," not "no egress restriction exists."

## The audit's own boundary

Apply enforcement-before-intent to yourself. Before inspecting the target, establish the effective tool and permission surface of this session.

- Do not broaden permissions or use permission-bypass modes to make the audit easier.
- Treat any write, shell, egress, or delegation capability you hold as authority the audit could exercise, even though this skill says not to.
- If nothing enforces read-only, continue passively and record the audit's own boundary as **Assumed, Not Enforced** or **Unverified**.
- If an inspection needs broader authority, record it under **Could Not Enumerate** instead of escalating.

Verify by observing the runtime, not by reading the configuration that claims to restrict it. In descending strength:

1. **Authoritative runtime enumeration** of the tools in force — **Observed**.
2. **A benign call test**: one harmless operation per capability class that should be unavailable; record how it fails. Absent and refused-at-call-time fail differently — **Observed**.
3. **Runtime self-description** — **Inferred** at best; a model cannot see deferred or lazily loaded tools.
4. **Configuration alone** — **Unverified**. It is the claim under test.

Enumerate by capability — **execution, mutation, egress, delegation, credential use** — not by tool name. Capability re-enters through unnamed tools, connected tool servers, other sessions, and paths that are not tools at all. Follow the adapter's procedure where it has one.

## Scope

Audit both surfaces when accessible, and never skip one silently:

1. **Application** — repository, dependencies, runtime manifests, infrastructure configuration, production paths.
2. **Agent harness** — the active harness, MCP/tool servers, hooks, skills, subagents, plugins, approval modes, filesystem and network reach, and user/global configuration.

For a large repository, state up front what is inspected exhaustively, what is sampled, what machine-level surfaces are accessible, and known limits. Do not imply exhaustive coverage where you sampled.

## Workflow

Each step has a reference holding its checklist; read it before the step. If a reference is missing, record that under **Could Not Enumerate**.

1. **Inventory** — `references/inventory.md`. Identify the active harness and read `harnesses/README.md` plus the matching adapter. No adapter means use the method above, not another harness's schema. If a skill or instruction originated in another harness or a neutral directory, ask: **what authority did it acquire when it crossed harnesses?** Restrictions do not travel with a portable skill. Finish the inventory before risk analysis; an incomplete inventory is itself a result.
2. **Platform checks** — `references/platform-checks.md`, only the sections for technologies actually found.
3. **Identity and reach** — `references/credentials-and-identity.md`. For each component: what identity it runs as, what explicit credentials and ambient authority it holds, and what systems, data, and actions that reaches.
4. **Boundaries and hostile input** — `references/boundaries-and-egress.md`. For every boundary described as read-only, sandboxed, internal, allowlisted, approved, or similar, find the deterministic mechanism. Find where **untrusted content + sensitive reach + outbound or action capability** coexist, even without code execution.
5. **Prototype and ownership drift** — `references/prototype-drift.md`.
6. **Report** — `references/reporting.md`. It defines the reach map, what qualifies as a finding, priority tiers, and output order. Lead with the widest-reach finding.

## Rules

- **Never expose secrets.** Do not print, echo, partially reveal, or summarize secret values. Reference credential name, source, consumer, and `file:line` only.
- **Reading a credential copies it.** A secret read into context may be persisted to transcripts, logs, caches, or telemetry by the harness, beyond any read-only profile. Identify credentials by name and location wherever the finding allows; where a value must be read, count every location it now occupies in the finding's reach.
- **Change nothing.** No edits, rotations, revocations, IAM or tool-server changes, installs, network changes, commits, or pull requests.
- **Do not prove reach destructively.** No state-changing requests to test credentials; no probing production to demonstrate access.
- **Do not assume harness equivalence.** Tool names, config keys, hook events, permission semantics, and skill locations vary by harness and version.
- **Do not overclaim completeness.** A repo or machine audit cannot prove organization-wide absence of shadow AI infrastructure.

The audit is complete when each method question is answered where evidence permits and marked **Unverified** where it does not.
