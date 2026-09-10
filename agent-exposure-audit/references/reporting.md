# Reporting Procedure

Read this reference when directed by `SKILL.md` before producing the final audit.

## Scale the report to the surface

Preserve the audit's evidence discipline and finding categories, but scale the presentation to the discovered surface.

- A small repository with two meaningful findings should receive a small report.
- Omit empty explanatory prose and collapse empty finding categories rather than padding the report.
- Keep **Could Not Enumerate** whenever material gaps exist.
- Do not compress away evidence level, reach, enforcement status, or the smallest closing change for a real finding.

The report should never be heavier than the evidence requires.

## Ranking

Rank by downstream blast radius. Lead with the single widest-reach finding.

Do not let a high CVSS score automatically outrank a lower-severity weakness attached to substantially broader downstream authority.

## Reach map

Use:

| Component | Exposure | Identity / Credential | Downstream Reach | Boundary | Evidence | Priority |
|---|---|---|---|---|---|---|

Where downstream reach cannot be verified, state exactly what external query or access would be required.

## Finding structure

For every finding provide:

### What and where
Use `file:line` when available.

### What it reaches
List downstream systems, data stores, APIs, cloud resources, repositories, administrative actions, and credentials without exposing secret values.

### Enforced or assumed
Use **Enforced**, **Assumed**, or **Unverified**, and name the mechanism when known.

### Evidence
Use **Observed**, **Inferred**, or **Unverified** and explain briefly.

### Why it matters
Describe realistic blast radius rather than repeating a generic severity label.

### Smallest closing change
Recommend the narrowest change that materially reduces reach: narrower credential, shorter lifetime, project-scoped/read-only identity, network restriction, removal of unnecessary tool, separate workload identity, external authorization check, or inventory registration. Avoid redesign unless required.

## Categories

### Reachable Now
Externally reachable or reachable from an untrusted input path.

### Over-Scoped
Legitimate access exists but authority materially exceeds need.

### Assumed, Not Enforced
A boundary is stated but no deterministic mechanism was found.

### Unowned
A live component lacks clear operational ownership.

## Could Not Enumerate

For each material enumeration gap state:

- what could not be inspected
- why
- what security question remains unanswered
- what access or command would be required to answer it

Where the gap exists because the audit's own tool boundary excludes the capability, say so plainly and name the **narrowest** capability that would close it. An unanswered question with its price stated is a result. Silently escalating authority to answer it is not.

Example:

> **Could Not Enumerate — secret history.** Repository history was not inspected: the audit runs without shell access, so `git log` and history-scanning tools were unavailable. Historical credential exposure is therefore **Unverified** — a previously committed and unrotated secret would not have been seen. Closing it requires read-only `git log`/`git show` over this repository and nothing further.

Do not silently turn missing evidence into a clean bill of health.

## Final output order

Use this order when the surface is large enough to justify separate sections. For small audits, combine or omit empty sections while preserving all material findings and gaps.

# Agent Exposure Audit

## Executive Summary
State the widest blast radius, most important assumed boundary, largest inventory gap, and overall confidence. Keep it short.

## Inventory
Present the inventory table.

## Reach Map
Present the consolidated blast-radius table.

## Reachable Now
Findings, if any.

## Over-Scoped
Findings, if any.

## Assumed, Not Enforced
Findings, if any.

## Unowned
Findings, if any.

## Could Not Enumerate
Material enumeration gaps and verification requirements, if any.

## Priority Actions
List the smallest changes that reduce the most blast radius first.
