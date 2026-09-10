# Prototype and Ownership Drift

Read this reference when directed by `SKILL.md` for prototype and ownership-drift analysis.

Look for experimental systems that quietly became infrastructure.

## Prototype drift

Check for:

- notebook-derived modules imported by production code
- files/modules named `demo`, `poc`, `prototype`, `scratch`, `tmp`
- test utilities imported into production
- development services referenced by production
- debug flags reachable in production
- local MCP servers that became globally available
- credentials created for pilots that remain active
- long-lived credentials without expiry
- dependencies floating across major versions
- abandoned orchestration frameworks still deployed

Do not classify a component as production merely because of its filename. Trace whether a live path imports, invokes, deploys, or depends on it.

## Ownership drift

Identify live components with unclear ownership. Look for:

- missing `CODEOWNERS` coverage
- no identifiable owning team
- no recent meaningful maintenance
- infrastructure with no documentation
- MCP servers or agent integrations configured globally but absent from project documentation

Treat lack of ownership as a risk multiplier, not proof of vulnerability.

The important pattern is:

> Nobody explicitly decided this should become infrastructure; it simply became infrastructure.
