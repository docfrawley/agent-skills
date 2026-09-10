# Platform-Specific Checks

Read this reference when directed by `SKILL.md` after inventory. Read only sections relevant to technologies actually discovered.

Harness-specific checks do **not** live here. Use the matching adapter under `../harnesses/`.

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

## MCP and other tool servers

For each server determine transport, local/remote status, scope, authentication, authorization, exposed tools, destructive actions, credential source, downstream systems, and whether access control is enforced by the server/provider rather than merely by client configuration.

Treat automatic approval or auto-enablement of project tool servers as a material trust decision. Ask whether exposed tools exceed what the current project actually needs.

Treat remote tool-server output as potentially untrusted content capable of influencing an agent that possesses other privileged tools.

## CI/CD

Inspect workflow permissions, secret availability, OIDC/workload identities, third-party actions, agent/model calls, PR/fork behavior, artifact handling, and whether AI tooling can modify workflows or deployment paths.

If an agent can write CI/CD workflow definitions, determine what identities, secrets, deployment credentials, package-publishing authority, or cloud roles those workflows can subsequently obtain. The agent's effective blast radius may include everything CI can reach, not merely repository files it can edit.
