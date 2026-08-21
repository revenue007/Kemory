# Manus Adapter

Use this adapter for Manus-specific mechanics. Follow `../SKILL.md` for core policy.

## Purpose

Use Manus as a worker/operator that can join a durable Kemory continuity session, retrieve authorized context, perform work, and save or propose a checkpoint.

## Loading the skill

At the start of any relevant Manus session, load or paste the Kemory Continuity Protocol instructions.

Recommended bootstrap prompt:

```text
Use the Kemory Continuity Protocol. Determine your effective Kemory permissions at runtime. Use namespace_hint and continuity_session_id. Retrieve authorized context before work and checkpoint before exit.
```

## Required metadata

Pass or preserve when possible:

```text
source_platform: Manus
source_agent_id: <agent identity>
source_platform_session_id: <Manus session/chat id if available>
namespace_hint: <project/customer/topic namespace>
continuity_session_id: <durable Kemory session id>
```

## Permission handling

Do not assume Manus is read-only or read/write. Determine effective Kemory permissions for the target namespace and action.

If write is unavailable, emit checkpoint and memory candidates.

## Upload/artifact handling

When Manus receives uploaded files, register them as Kemory artifacts if permitted. If not permitted, reference them only as local-session inputs and produce artifact-registration candidates.

Do not store whole documents as ordinary memories by default.
