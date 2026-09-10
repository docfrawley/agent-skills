# Pi Adapter

Use this adapter when Pi is the active harness.

Pi documents the Agent Skills standard and supports both native Pi skill locations and neutral `.agents/skills/` locations. Verify paths and settings against the installed version when possible.

## Skill discovery

Inspect, where accessible:

- `.pi/skills/`
- `.agents/skills/`
- `~/.pi/agent/skills/`
- `~/.agents/skills/`
- package-provided skills
- settings that add skill files/directories
- CLI-provided skill paths

Pi may discover skills recursively. Inventory the effective set available to the current project/session rather than only one directory.

## Tool and workflow authority

Inspect current Pi configuration, profiles, packages, and workflows for:

- enabled tool set
- shell/terminal authority
- filesystem reach
- network/tool-server access
- saved workflows or profiles that change tool availability
- package-provided code or extensions
- inherited environment variables and credentials
- project/user scope and trust state

Where Pi configuration supports an explicit tool list, treat that as the candidate enforcement layer and verify the active runtime actually uses it.

## Cross-harness skill loading

If Pi is loading skills from neutral or another harness's directories, do not assume the originating harness's tool restrictions or permission semantics transfer with the skill. Re-evaluate the skill under Pi's active tool and process model.
