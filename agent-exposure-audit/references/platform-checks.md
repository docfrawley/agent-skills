# Platform-Specific Checks

Read this reference when directed by `SKILL.md` after inventory. Read only sections relevant to technologies actually discovered.

## FastAPI

Check whether agent-invoked endpoints receive the same authentication and authorization protections as equivalent human-invoked endpoints.

Look for internal routes, service tokens, routers mounted without standard dependencies, endpoints excluded from auth middleware, weak shared secrets, and authorization performed only by upstream assumptions.

## Next.js / React

Inspect server actions, route handlers, API proxies, model/agent endpoints, and client bundles.

Flag provider credentials or sensitive configuration exposed through `NEXT_PUBLIC_*`, client-side environment injection, bundled configuration, or server-to-client serialization.

## Kubernetes

Inspect service accounts, RBAC, NetworkPolicies, secret mounts, namespace boundaries, workload identity, privileged containers, host mounts, and whether the pod can access cloud metadata or other cluster services beyond its purpose.

## Docker / Compose

Inspect privileged mode, Docker socket mounts, host networking, host filesystem mounts, inherited environment, exposed ports, container user, capabilities, and network segmentation.

Docker socket access is high-impact because it may effectively grant host-level control.

## Cloud infrastructure

Trace workload identities and network boundaries from infrastructure-as-code. Distinguish declared policy from effective policy when external control-plane verification is unavailable.

## Claude Code

Inspect project and user settings, agent tool grants, hooks, skills, plugins, filesystem reach, shell capability, and whether permissions are global or project-specific.

Pay special attention to wildcard tool grants and hooks that execute shell commands.

## MCP

For each server determine transport, local/remote status, scope, authentication, authorization, exposed tools, destructive actions, credential source, downstream systems, and whether access control is enforced by the server/provider rather than merely by the client configuration.

## CI/CD

Inspect workflow permissions, secret availability, OIDC/workload identities, third-party actions, agent/model calls, PR/fork behavior, artifact handling, and whether AI tooling can modify workflows or deployment paths.
