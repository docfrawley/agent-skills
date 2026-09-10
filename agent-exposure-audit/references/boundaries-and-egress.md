# Boundaries, Containment, and Egress

Read this reference when directed by `SKILL.md` for boundary, containment, and egress analysis.

The goal is to find where a security boundary is **described** and determine what actually enforces it.

## Asserted boundaries

Search code, comments, configuration keys, environment variables, documentation, and infrastructure definitions for terms such as:

- `read.?only`, `readonly`
- `sandbox`, `isolated`
- `restricted`, `limited`
- `internal.?only`, `private`
- `trusted`, `safe`
- `no.?write`, `dry.?run`
- `allowlist`, `denylist`

For every relevant claim:

1. Identify the asserted boundary.
2. Identify the enforcement mechanism.
3. Determine whether enforcement exists at the appropriate layer.
4. Classify the evidence.
5. Record any gap.

A prompt is not a permission boundary. A comment is not a network boundary. A configuration name is not authorization.

## Tool permissions

Determine whether restrictions are enforced by runtime permissions, MCP/server authorization, OS/container permissions, or only by a system prompt/documentation.

Prompt-level restrictions are behavioral guidance, not deterministic security controls.

## Sandbox claims

If code claims to be sandboxed, look for actual isolation mechanisms such as containers/VMs, separate OS users, filesystem namespaces, seccomp, capability dropping, resource limits, network namespaces, restricted mounts, and ephemeral environments.

A function named `run_sandboxed` is not evidence of a sandbox.

Also inspect what the sandbox can reach outside itself. A sandbox with unrestricted credentials or egress may contain filesystem effects while failing to contain downstream effects.

## Egress

Determine whether agent processes can connect to arbitrary destinations. Inspect network policies, firewall rules, proxies, VPC/security-group rules, container networking, Kubernetes NetworkPolicies, DNS controls, and application-level destination allowlists.

Distinguish application intent from network enforcement.

Treat allowlisted destinations as **capability grants**. For each, consider what functions are reachable through that destination and whether attacker-controlled inputs, credentials, redirects, alternate hosts, or proxy behavior expand effective capability.

## HTTP assumptions

Search for controls that assume GET is inherently read-only, POST is inherently dangerous, or blocking one method prevents mutation. Check actual application/framework behavior rather than relying on HTTP semantics alone.

## Dynamic execution

Search for `eval`, `exec`, `pickle.loads`, unsafe YAML loading, dynamic imports, shell execution, template execution, code validators, and generated-code execution.

Prioritize paths where model output, MCP output, retrieved content, uploaded files, or user-controlled input can reach dynamic execution.

Ask: **If this input becomes hostile, what identity does the resulting code inherit?** That identity determines blast radius.
