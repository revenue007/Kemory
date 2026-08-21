# OpenAI / ChatGPT Adapter

Use this adapter for ChatGPT or OpenAI-agent mechanics. Follow `../SKILL.md` for core policy.

## Purpose

Use ChatGPT/OpenAI for synthesis, ideation, structured drafting, GTM copy, analysis, and continuation from Kemory checkpoints.

## Loading the skill

Load or paste the Kemory Continuity Protocol at the start of relevant sessions. If persistent custom instructions are available, add a short bootstrap instruction that tells the agent to use the protocol for Kemory-backed work.

## Required metadata

```text
source_platform: ChatGPT/OpenAI
source_agent_id: <agent identity>
source_platform_session_id: <conversation id if available>
namespace_hint: <project/customer/topic namespace>
continuity_session_id: <durable Kemory session id>
```

## Permission handling

Do not assume ChatGPT/OpenAI can write. Determine effective Kemory permissions at runtime.

If Kemory tools are unavailable, produce structured checkpoint and memory candidates for later ingestion.

## Fallback behavior

When direct Kemory access is unavailable, start responses with recovered/provided context only. End meaningful work with a checkpoint candidate that can be copied into Kemory by a permitted agent or human.
