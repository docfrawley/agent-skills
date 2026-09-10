# Credentials, Identity, and Reach

Read this reference when directed by `SKILL.md` for identity, credential, and downstream-reach analysis.

For every inventory item answer: **What can this actually reach?**

## Sources

Inspect, where relevant:

- `.env`, `.env.*`, `.envrc`
- settings modules and Pydantic `BaseSettings`
- `config/`
- CI/CD configuration and `.github/workflows/*.yml`
- Docker `ENV` / `ARG`
- Compose `environment:` and `secrets:`
- Kubernetes secrets, service accounts, workload identities
- Terraform and other infrastructure-as-code
- cloud profile references
- credential mounts
- secret-manager references
- parent-process environment inheritance

Never print secret values.

For every credential or identity record:

- credential/identity name
- consuming process
- source
- explicit or ambient
- intended purpose
- known or inferred scope
- rotation / expiry evidence
- downstream systems
- evidence classification

## Ambient authority

Do not limit the audit to explicit keys. Check for authority inherited through:

- AWS instance profiles and task roles
- Azure managed identities
- GCP service accounts
- Workload Identity Federation
- Kubernetes service accounts
- mounted cloud credentials
- Docker socket access
- host SSH agents
- credential helpers
- parent-process environment
- local cloud CLI sessions
- GitHub CLI authentication

An agent may hold no visible secret and still possess broad authority.

## Scope questions

### Cloud

Determine where evidence permits: static key vs temporary identity, user credential vs workload role, actions/resources allowed, wildcard actions/resources, production access, and cross-account/project reach.

Wildcard permissions are findings when they materially exceed demonstrated need.

### Database

Determine read/write/DDL capability, environment, tenant isolation, reachable schemas/tables, access beyond purpose, and whether a separate least-privilege identity exists.

### Model providers

Determine organization-level vs project-scoped key, spend limits, model restrictions, data-access implications, administrative capability, and access to provider-side files or other stored resources where relevant.

### Source control

Determine read/write scope, repositories reachable, PR creation, direct push, workflow modification, secrets access, branch-protection implications, and organization administration.

### Third-party services

Ask: **What does this identity authorize beyond the one action the code currently uses?**

The code path demonstrates usage, not necessarily the limit of authority.

## Secret history

Inspect both working tree and version-control history for credential exposure. Prefer `gitleaks` or `trufflehog` when already available. Do not install tools automatically.

If unavailable, use targeted read-only repository-history inspection for likely secret patterns.

Distinguish:

- currently present
- previously committed and confirmed rotated
- previously committed with rotation unknown
- previously committed and apparently still live

A rotated committed secret is historical exposure, not current reach. If rotation cannot be verified, classify as **Unverified**.
