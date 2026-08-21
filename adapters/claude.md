# Claude Adapter

Use this adapter for Claude-specific mechanics. Follow `../SKILL.md` for core policy.

## Purpose

Use Claude for long-context work, implementation planning, code/spec drafting, and detailed continuation from Kemory checkpoints.

## Loading the skill

Load the Kemory Continuity Protocol at the start of relevant Claude sessions or include it in Claude project instructions.

Recommended bootstrap prompt:

```text
Use the Kemory Continuity Protocol. Continue from the latest checkpoint for the provided namespace/session. Determine effective Kemory permissions before any write.
```

## Required metadata

```text
source_platform: Claude
source_agent_id: <agent identity>
source_platform_session_id: <Claude conversation/project session id if available>
namespace_hint: <project/customer/topic namespace>
continuity_session_id: <durable Kemory session id>
```

## Permission handling

Do not assume Claude can write. Determine effective Kemory permissions at runtime.

If write is unavailable, output structured memory and checkpoint candidates.

## Suggested use

Claude is often useful for:

- turning checkpoints into implementation plans;
- drafting specs;
- reviewing code or architecture;
- continuing long-form product/design work.

These are role suggestions, not permission assumptions.
