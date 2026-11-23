# Reporting Procedure

Read this reference when directed by `SKILL.md` before producing the final audit.

## What is a finding

Every finding must name all three:

1. **A concrete reachable thing** — a credential, identity, tool, data store, repository, or action, at `file:line` or a named config location.
2. **A realistic trigger** — who or what could cause it to be used: hostile content the agent actually ingests, a compromised dependency or tool server, an auto-run hook, a mistaken agent action.
3. **The consequence** — what is read, changed, sent, or spent that the owner would not have approved.

If you cannot name all three, it is not a finding. Record it under **Could Not Enumerate** if a missing fact blocks it, or drop it.

These are **not** findings on their own:

- A best practice that is absent with no reachable consequence ("no rotation policy," "MCP server is not pinned") unless you can state what that absence exposes.
- Generic defense-in-depth advice.
- "The agent could be prompt-injected." Injection matters only where a specific untrusted source meets a specific sensitive reach and a specific outbound or action path.
- The user's own intended authority. An agent holding the developer's credentials to do the developer's work is the design; it becomes a finding when that authority materially exceeds the work, or is reachable by something less trusted than the developer.
- A boundary that is assumed but guards nothing of consequence.
- A configuration key or capability that exists but is unused, disabled, or unreachable in the effective configuration.

When in doubt, prefer fewer, sharper findings. A finding the owner cannot act on dilutes the ones they can.

## Priority

Rank by downstream blast radius, not by CVSS. Lead with the single widest-reach finding. Calibrate against these anchors:

- **P0** — reachable now, from untrusted input or with no approval in the path, to production systems, organization-wide credentials, or arbitrary code execution as the user. *Example: an auto-approved tool that ingests web content, alongside a token with org-wide repository write.*
- **P1** — broad authority one plausible step away: a credential or identity scoped well beyond need, reachable by an agent that ingests untrusted content, or gated only by an approval that is easily pre-approved. *Example: a cloud admin profile inherited by every agent session.*
- **P2** — real but bounded: limited scope, uncommon preconditions, or a single non-production resource. *Example: a project-scoped read/write key to a staging database.*
- **P3** — hygiene with a nameable but small consequence, or a risk multiplier such as missing ownership on a live component.

Rank by how many independent events must occur before the reach is used, not by how bad the end state is. A path that needs a third party compromised first — a package publisher, a tool-server operator, a marketplace — is **P1**, not P0, even when it then runs with no approval: one plausible step away. It is P0 only when that upstream step has already happened, or when the component can be driven by content the agent ingests in normal use.

Priority cannot exceed the evidence: an **Inferred** reach claim cannot be P0 unless the inference is the only missing link and you say so. **Unverified** reach is not ranked; it goes under Could Not Enumerate with the query that would resolve it.

## Reach map

| Component | Exposure | Identity / Credential | Downstream Reach | Boundary | Evidence | Priority |
|---|---|---|---|---|---|---|

The map answers: **if this component is compromised, manipulated, or misbehaves, what becomes reachable next?** Where downstream reach cannot be verified, state the external query or access that would verify it. Never include secret values.

## Finding structure

For each finding:

- **What and where** — `file:line` when available.
- **What it reaches** — downstream systems, data, APIs, cloud resources, repositories, administrative actions, credentials (names only).
- **Enforced or assumed** — **Enforced**, **Assumed**, or **Unverified**, naming the mechanism when known.
- **Evidence** — **Observed**, **Inferred**, or **Unverified**, with a one-line basis.
- **Why it matters** — the realistic blast radius, not a generic severity label.
- **Smallest closing change** — the narrowest change that materially cuts reach: a narrower or shorter-lived credential, a project-scoped or read-only identity, a network restriction, removing an unneeded tool, a separate workload identity, an external authorization check, or registering the component in inventory. Avoid redesign unless required.

## Categories

- **Reachable Now** — reachable externally or from an untrusted input path.
- **Over-Scoped** — legitimate access whose authority materially exceeds need.
- **Assumed, Not Enforced** — a stated boundary with no deterministic mechanism behind it.
- **Unowned** — a live component without clear operational ownership.

## Could Not Enumerate

For each material gap: what could not be inspected, why, the security question left open, and the access or capability that would answer it. Where the gap exists because the audit's own boundary excludes a capability, say so and name the narrowest capability that would close it. An unanswered question with its price stated is a result; silently escalating to answer it is not. Never turn missing evidence into a clean bill of health.

## Output

Scale the report to the surface. A small repository with two real findings gets a short report. Omit empty categories and explanatory padding, but never compress away evidence level, reach, enforcement status, or the closing change for a real finding. Keep Could Not Enumerate whenever material gaps exist.

Order, omitting empty sections:

1. **Executive Summary** — widest blast radius, most important assumed boundary, largest inventory gap, overall confidence. A few lines.
2. **Inventory** table.
3. **Reach Map**.
4. Findings by category, each sorted by priority: **Reachable Now**, **Over-Scoped**, **Assumed, Not Enforced**, **Unowned**.
5. **Could Not Enumerate**.
6. **Priority Actions** — the smallest changes that remove the most blast radius, first.
