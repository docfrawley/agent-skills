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

**Configuration names in this section are examples, not a schema.** They are version-sensitive and will drift: keys get renamed, hook events are added and retired, and settings move between scopes. Verify them against the installed version or current authoritative documentation rather than treating this list as exhaustive or permanent.

**Failure to find a named key is not itself a finding.** It usually means the version differs from the one this reference was written against. Audit the underlying capability instead, and let the names be a starting point for finding it:

- filesystem reach
- shell authority
- permission bypass
- automatic or pre-interaction execution
- MCP enablement and scope
- inherited credentials
- network reach

Inspect project, local, user, and managed settings where accessible. Determine the effective result after scope/precedence/merging, not merely the value in one settings file.

Check current supported controls including:

- `permissions.allow`, especially wildcard or broad `Bash(...)` rules
- `permissions.ask`
- `permissions.deny`
- `permissions.defaultMode`, especially `bypassPermissions`
- `permissions.additionalDirectories`
- `enableAllProjectMcpServers`
- `enabledMcpjsonServers`
- settings or policy that alter skill shell execution
- CLI/session overrides such as `--allowedTools`, `--disallowedTools`, `--permission-mode`, and `--add-dir` when observable

Treat broad Bash authorization as mutation-capable unless the command pattern itself constrains it to read-only behavior. A behavioral instruction saying “do not edit” does not make unrestricted Bash read-only.

Inspect hooks by lifecycle event, matcher, handler type, and authority. Pay special attention to:

- `SessionStart` and `Setup`, because they can run before ordinary interaction proceeds
- `UserPromptSubmit`, because it can alter/block prompt flow
- `PreToolUse` and `PermissionRequest`, because they sit on the tool-execution path
- `PostToolUse`, because it processes tool output
- configuration/instruction-loading events such as `ConfigChange` and `InstructionsLoaded`

For `SessionStart`, inspect whether output is injected into model context, whether environment variables are persisted for later Bash commands, what inherited credentials are visible, and whether the hook is project-, user-, plugin-, or managed-sourced.

Inspect agent tool grants, skills, plugins, filesystem reach, shell capability, and whether permissions are global or project-specific.

## MCP

For each server determine transport, local/remote status, scope, authentication, authorization, exposed tools, destructive actions, credential source, downstream systems, and whether access control is enforced by the server/provider rather than merely by the client configuration.

Treat automatic approval of project MCP servers as a material trust decision. For every enabled server, ask whether its exposed tools exceed what the current project actually needs.

Also treat remote MCP output as potentially untrusted input capable of influencing an agent that possesses other privileged tools.

## CI/CD

Inspect workflow permissions, secret availability, OIDC/workload identities, third-party actions, agent/model calls, PR/fork behavior, artifact handling, and whether AI tooling can modify workflows or deployment paths.

If an agent can write CI/CD workflow definitions, determine what identities, secrets, deployment credentials, package-publishing authority, or cloud roles those workflows can subsequently obtain. The agent's effective blast radius may therefore include everything CI can reach, not merely the repository files it can edit.
