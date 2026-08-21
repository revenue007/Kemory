# Perplexity Adapter

Use this adapter for Perplexity-specific mechanics. Follow `../SKILL.md` for core policy.

## Purpose

Use Perplexity for research-oriented workflows that may feed Kemory with sourced findings, checkpoint candidates, or memory candidates.

## Loading the skill

Load or paste the Kemory Continuity Protocol at the start of relevant research sessions.

## Required metadata

```text
source_platform: Perplexity
source_agent_id: <agent identity>
source_platform_session_id: <session id if available>
namespace_hint: <project/customer/topic namespace>
continuity_session_id: <durable Kemory session id>
```

## Permission handling

Do not assume Perplexity is read-only or write-capable. Determine effective Kemory permissions at runtime.

For public-web research, strongly prefer sourced memory candidates unless this agent has explicit write permission and the workflow authorizes durable writeback.

## Research provenance

Every proposed memory from research should include:

```text
source_url:
source_title:
claim:
confidence:
retrieved_at:
```

Do not write unsupported public-web claims into durable memory.
