# Reporting Procedure

Read this reference when directed by `SKILL.md` before producing the final audit.

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

Always close with enumeration gaps. For each state:

- what could not be inspected
- why
- what security question remains unanswered
- what access or command would be required to answer it

Do not silently turn missing evidence into a clean bill of health.

## Final output order

# Agent Exposure Audit

## Executive Summary
State the widest blast radius, most important assumed boundary, largest inventory gap, and overall confidence. Keep it short.

## Inventory
Present the inventory table.

## Reach Map
Present the consolidated blast-radius table.

## Reachable Now
Findings.

## Over-Scoped
Findings.

## Assumed, Not Enforced
Findings.

## Unowned
Findings.

## Could Not Enumerate
Enumeration gaps and verification requirements.

## Priority Actions
List the smallest changes that reduce the most blast radius first.
