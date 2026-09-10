# Boundaries, Containment, and Egress

Read this reference when directed by `SKILL.md` for boundary, containment, egress, and hostile-input analysis.

The goal is to find where a security boundary is described and determine what actually enforces it — including cases where no code execution is required for an attacker to exploit agent authority.

## Asserted boundaries

Search code, comments, configuration keys, environment variables, documentation, harness policy, and infrastructure definitions for terms such as:

- `read.?only`, `readonly`
- `sandbox`, `isolated`
- `restricted`, `limited`
- `internal.?only`, `private`
- `trusted`, `safe`
- `no.?write`, `dry.?run`
- `allowlist`, `denylist`
- `approval`, `ask`, `confirm`

For every relevant claim:

1. Identify the asserted boundary.
2. Identify the enforcement mechanism.
3. Determine whether enforcement exists at the appropriate layer.
4. Classify the evidence.
5. Record any gap.

A prompt is not a permission boundary. A comment is not a network boundary. A configuration name is not authorization.

## Tool permissions

Determine whether restrictions are enforced by harness/runtime permissions, tool-server authorization, OS/container permissions, network controls, or only by a system prompt/documentation.

Prompt-level restrictions are behavioral guidance, not deterministic security controls.

Pay special attention to combinations of individually legitimate tools that create broader authority when composed: sensitive-data read + network egress, source-control write + CI secrets, filesystem read + external messaging, or browser/retrieval access + privileged actions.

## Prompt injection and hostile-content reach

Treat prompt injection as a reach problem, not only a prompt-quality problem.

> **Untrusted content + sensitive reach + outbound/action capability = prompt-injection blast radius**

Look for agents that ingest content controlled outside the relevant trust boundary, including:

- web pages and search results
- retrieved documents and RAG corpora
- email, chat, tickets, issue bodies, PR text, and comments
- MCP/tool-server results or remote content
- uploaded files
- database records editable by less-trusted users
- third-party API responses
- generated or user-supplied documentation/instructions

For each path ask:

1. Can untrusted content become model context or otherwise influence planning/tool selection?
2. At that moment, what sensitive data, credentials, tools, or identities are available?
3. Does the agent have an outbound or state-changing path?
4. What deterministic control prevents hostile content from causing disclosure or action?
5. Is that control enforced outside the model, or primarily by instructions such as "ignore malicious prompts"?

Do not require `eval`, shell execution, or arbitrary code execution before treating this as material exposure. A manipulated agent can misuse legitimate tools and permissions.

Where possible, identify trust transitions explicitly:

**untrusted content → model context → privileged identity/tool → downstream system**

## Sandbox claims

If code or harness policy claims to be sandboxed, look for actual isolation mechanisms such as containers/VMs, separate OS users, filesystem namespaces, seccomp, capability dropping, resource limits, network namespaces, restricted mounts, and ephemeral environments.

A function or mode named `sandboxed` is not evidence of effective containment.

Also inspect what the sandbox can reach outside itself. A sandbox with unrestricted credentials or egress may contain filesystem effects while failing to contain downstream effects.

## Egress

Determine whether agent processes can connect to arbitrary destinations. Inspect network policies, firewall rules, proxies, VPC/security-group rules, container networking, Kubernetes NetworkPolicies, DNS controls, browser/web tools, tool-server transports, and application-level destination allowlists.

Distinguish application intent from network enforcement.

Treat allowlisted destinations as capability grants. For each, consider what functions are reachable through that destination and whether attacker-controlled inputs, credentials, redirects, alternate hosts, or proxy behavior expand effective capability.

For an agent that can read sensitive data, egress itself can be the final step of a prompt-injection attack even when the process never executes attacker-supplied code.

## Approval boundaries

Do not assume "asks before action" is a security boundary without verifying what actions require approval, whether approvals can be bypassed or pre-approved, which tools are exempt, and whether hooks/automation can execute outside the approval path.

## HTTP assumptions

Search for controls that assume GET is inherently read-only, POST is inherently dangerous, or blocking one method prevents mutation. Check actual application/framework behavior rather than relying on HTTP semantics alone.

## Dynamic execution

Search for `eval`, `exec`, `pickle.loads`, unsafe YAML loading, dynamic imports, shell execution, template execution, code validators, and generated-code execution.

Prioritize paths where model output, tool-server output, retrieved content, uploaded files, or user-controlled input can reach dynamic execution.

Ask: **If this input becomes hostile, what identity does the resulting code inherit?** That identity determines blast radius.
