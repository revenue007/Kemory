---
name: kemory-continuity-protocol
description: Use when an AI agent needs Kemory for persistent memory, same-agent inter-session continuity, cross-agent handoff, session resume, uploaded document continuity, or durable memory writeback across Manus, Claude, ChatGPT/OpenAI, Gemini, Perplexity, and other agents. Behavior is permission-derived at runtime between the specific agent identity and Kemory; platform adapters define mechanics only.
---

# Kemory Continuity Protocol

Use Kemory as the durable continuity layer across:

- multiple sessions in the same agent;
- handoffs between different agents;
- project/customer/topic work;
- uploaded documents and artifacts;
- durable decisions, preferences, constraints, and open loops.

Do not rely on local chat history as the system of record. Treat the local agent session as temporary. Treat Kemory as the shared memory and handoff plane.

Core loop:

```text
hydrate -> work -> checkpoint -> write/propose memory -> handoff/resume
```

## Core principle

Kemory behavior is **permission-derived, not platform-derived**.

Do not assume capabilities from the platform name. The same platform may have different permissions in different agents, namespaces, teams, orgs, or sessions. Always determine the effective permission between this specific agent identity and Kemory before reading, writing, updating, promoting, deleting, merging, or broadening visibility.

Platform adapters may define mechanics. They must not define authority.

## Required runtime fields

Before meaningful work, identify or infer:

```text
user_id
org_id
namespace_hint
continuity_session_id
source_platform
source_agent_id
permissions_mode
task_objective
```

If `namespace_hint` or `continuity_session_id` is unclear, infer the best candidate from the request and available context. Ask only if ambiguity could cause a write to the wrong project, customer, or visibility scope.

## Namespaces

The namespace describes what the work is about.

Prefer project, customer, topic, or user namespaces:

```text
project:kemory
project:kanvas
gtm:kemory
customer:eversheds
customer:park-wealth
user:preferences
research:competitors
```

Avoid platform-based namespaces unless the platform itself is the topic. Do not use `manus:kemory`, `claude:kemory`, or `chatgpt:kemory` for ordinary Kemory project work.

Store platform identity as metadata:

```text
source_platform: Manus
source_agent_id: ...
source_platform_session_id: ...
```

Always provide `namespace_hint` when possible. Use explicit hints over inferred classification.

If unsure:

1. Prefer the most specific project/customer namespace.
2. Use `user:preferences` only for stable user preferences.
3. Use `inbox:<platform>` only as a fallback.
4. Never invent many near-duplicate namespaces.
5. If namespace is uncertain but the write is important, propose a memory candidate instead of writing.

## Continuity sessions

A continuity session is the durable Kemory session/thread that survives beyond one platform chat.

Use it for both:

- same-agent continuation across sessions;
- cross-agent continuation across platforms.

Example:

```text
continuity_session_id: kemory-cross-agent-continuity-skill-2026-08-21
namespace_hint: project:kemory
```

The continuity session is not the same as the platform's local chat ID.

## Start-of-session workflow

At the start of a relevant session:

1. Determine whether this is a new continuity session or a continuation.
2. Set or infer `namespace_hint`.
3. Set or infer `continuity_session_id`.
4. Determine effective Kemory permissions for the target namespace, session, resource, and action.
5. Retrieve Kemory context before doing substantive work if read access exists.
6. Prefer the narrowest authorized namespace first.
7. Broaden only if the task clearly requires cross-namespace context or the user explicitly asks.

Retrieve in this order when available and authorized:

```text
1. Latest handoff checkpoint for continuity_session_id
2. Session summary / rolling session context
3. Cumulative summary for the namespace
4. Relevant memories for the task objective
5. Relevant artifacts or document chunks
6. Broader user/project context only if needed
```

If no continuity session is provided, search for recent active checkpoints in the likely namespace.

## Retrieval guidance

Use the right Kemory read surface for the task.

| Need | Retrieval behavior |
|---|---|
| Exact fact or quote | Retrieve raw/L1 memory |
| Current preference/config | Prefer concept/current-value memory |
| Resume previous work | Retrieve latest checkpoint, session summary, cumulative summary |
| Long live session | Use rolling session digest plus latest raw exchanges |
| Project overview | Retrieve namespace summary |
| Cross-project/user context | Retrieve user context; use synthesized context only when worth the cost |
| Uploaded document Q&A | Search artifacts/chunks; do not rely on whole-document summaries only |

Always preserve source/provenance when using retrieved context.

## Permission-aware behavior

Before any Kemory action, determine the agent's effective permission for:

- target namespace;
- intended action;
- relevant memory, artifact, or checkpoint;
- visibility scope;
- current user/org/team context;
- time/rate conditions when applicable.

Use the strongest allowed behavior and the safest fallback.

### No read access

- Do not retrieve memory.
- Ask the user to connect or authorize access, or proceed without Kemory context.
- Do not claim Kemory has no relevant memory if access is unavailable.

### Read-only access

- Retrieve authorized context.
- Use it for the task.
- Do not write, update, delete, promote, merge, or broaden memory.
- At the end, produce structured memory candidates and checkpoint candidates.

### Read + write access

- Retrieve before work.
- Search for duplicates before writing.
- Update existing memories where appropriate.
- Store only durable outcomes.
- Create/update handoff checkpoints.
- Use expiry for temporary facts.
- Preserve provenance.

### Artifact read access

- Retrieve and use authorized artifacts/chunks.
- Do not upload or attach new artifacts unless artifact write access exists.

### Artifact write access

- Register uploaded documents as artifacts in the correct namespace/session.
- Do not store entire documents as regular memories.
- Promote/store only durable findings if memory write access also exists.

### Elevated/admin access

- Still use least privilege.
- Do not merge namespaces, change permissions, delete memory, or broaden visibility unless explicitly requested.

## During-session workflow

While working:

1. Keep the same `namespace_hint` and `continuity_session_id` unless the task materially changes.
2. Use retrieved Kemory context as grounding.
3. Track decisions, constraints, assumptions, open questions, and artifacts.
4. If the user uploads a document, treat it as an artifact first.
5. Do not store entire documents as ordinary memories.
6. Promote or store only durable findings from documents.
7. Do not merge namespaces or broaden visibility without explicit authorization.
8. If the task changes materially, reassess namespace, continuity session, and permissions.

## Uploaded document workflow

When a user uploads or references a document:

1. Attach or register the document as an artifact in the relevant namespace/session if artifact write access exists.
2. Use document chunks or artifact search for immediate context if artifact read access exists.
3. Cite or reference the artifact/chunk when using it.
4. Promote only useful, cited, durable findings into memory if memory write access exists.
5. Do not store the entire raw document as one memory unless explicitly instructed.
6. If the document produces temporary operational context, assign expiry.

Good durable document-derived memories include:

- requirements;
- decisions;
- constraints;
- customer facts;
- architecture facts;
- reusable insights;
- risks or blockers;
- final conclusions.

Bad memory candidates include:

- raw extracted text;
- every section summary;
- duplicate facts;
- speculative interpretations;
- transient scratch notes.

## What to store

Store or propose memory for:

- durable decisions;
- stable user preferences;
- customer facts;
- architecture choices;
- project status changes;
- reusable instructions;
- constraints;
- risks;
- blockers;
- open loops;
- final conclusions;
- handoff checkpoints.

Do not store:

- every turn;
- intermediate reasoning;
- scratch calculations;
- speculative guesses;
- duplicate summaries;
- whole uploaded documents;
- low-confidence namespace guesses;
- sensitive content into broader visibility than the source.

## Writeback decision tree

Before writing memory, ask:

```text
1. Is this durable?
2. Will future sessions or agents benefit from it?
3. Is the namespace clear?
4. Is visibility safe?
5. Does a similar memory already exist?
6. Should this update an existing memory?
7. Should this expire?
8. Is there source/provenance?
9. Does this agent have write permission?
```

If any answer is unclear, produce a memory candidate instead of writing.

## Checkpoint workflow

At the end of any meaningful work block, create or propose a handoff checkpoint.

This is required for both same-agent inter-session continuity and cross-agent handoff.

Checkpoint format:

```text
Handoff checkpoint

namespace:
continuity_session_id:
source_platform:
source_agent_id:
permissions_mode:
created_at:

objective:
current_state:

completed_since_last_checkpoint:
- ...

decisions:
- ...

constraints:
- ...

open_loops:
- ...

artifacts_and_sources:
- ...

memory_updates_made:
- ...

memory_candidates_if_read_only:
- ...

recommended_next_action:

confidence:
uncertainties:
```

The checkpoint should let another session continue without rereading the entire chat.

## Resume workflow

When asked to continue work:

1. Retrieve the latest checkpoint for the provided `continuity_session_id`.
2. If no session ID is provided, find the most relevant recent checkpoint in the namespace.
3. Retrieve supporting session summary, cumulative summary, recent raw exchanges, and artifacts.
4. Briefly restate the recovered state.
5. Continue from the checkpoint, not from scratch.
6. At the end, write or propose a new checkpoint.

## Cross-agent handoff behavior

When handing off to another agent, include:

```text
handoff_target:
why_this_agent:
what_context_to_retrieve:
what_to_do_next:
what_not_to_repeat:
known_uncertainties:
```

## Visibility policy

Use least privilege.

Preferred visibility order:

```text
private -> team -> org
```

Do not move private or narrow-context information into a broader namespace or visibility scope without explicit authorization.

Do not treat namespace names such as `shared` or `agent-private` as sufficient security boundaries. Use actual permissions and visibility controls.

## Platform adapters

Use platform adapters for mechanics only.

Adapters may specify:

- how to load this skill;
- how to call Kemory MCP or REST;
- how to pass `namespace_hint` and `continuity_session_id`;
- how to handle uploads;
- how to format handoff output;
- how to proceed when tools are unavailable.

Adapters must not assume read/write authority from platform identity. Authority comes only from effective Kemory permissions for this agent, namespace, resource, and action.

## Output formats

### Session-start acknowledgement

```text
Recovered context:
- Namespace:
- Continuity session:
- Effective permissions:
- Last checkpoint:
- Current objective:
- Open loops:
- Relevant artifacts:
```

Keep it brief unless the user asks for detail.

### End-of-session checkpoint acknowledgement

```text
Checkpoint saved/proposed:
- Objective:
- Current state:
- Decisions:
- Open loops:
- Next action:
```

### Memory candidate

```text
Memory candidate:
- Namespace:
- Continuity session:
- Type:
- Proposed memory:
- Source:
- Confidence:
- Expiry:
- Reason:
- Updates existing memory:
- Visibility suggestion:
```

## Examples

### Same agent, new session

User:

```text
Continue the Kemory skill work from yesterday.
```

Agent behavior:

```text
1. Infer namespace: project:kemory.
2. Search latest checkpoints for Kemory skill work.
3. Retrieve session/cumulative context.
4. Summarize recovered state.
5. Continue work.
6. Save/propose updated checkpoint.
```

### Cross-agent handoff

User:

```text
Continue in Claude what I started in Manus.
```

Agent behavior:

```text
1. Ask for session ID only if not inferable.
2. Retrieve latest Manus checkpoint.
3. Retrieve supporting Kemory memories and artifacts.
4. Continue from current_state and recommended_next_action.
5. Save/propose new checkpoint.
```

### Read-only agent

User:

```text
Use Kemory context and draft the GTM page.
```

Agent behavior:

```text
1. Retrieve GTM/Kemory context if read access exists.
2. Draft the page.
3. Do not write memory if write access is absent.
4. Produce memory candidates for durable decisions discovered during work.
5. Produce handoff checkpoint candidate.
```

### Uploaded document

User:

```text
Use this PDF and continue the customer strategy.
```

Agent behavior:

```text
1. Attach/register PDF as artifact if permitted.
2. Use namespace/customer hint.
3. Search chunks for relevant information if permitted.
4. Use cited findings in work.
5. Promote/store only durable conclusions if permitted.
6. Checkpoint open loops and next action.
```

## Non-goals

Do not use this skill to:

- replace Kemory's permission model;
- bypass gatekeeper rules;
- merge namespaces automatically;
- store raw documents as memories;
- memorize all chat turns;
- use platform identity as the main namespace;
- write public-web claims without provenance;
- broaden access without explicit authorization.

## Short operating rule

```text
Retrieve narrowly.
Work with provenance.
Checkpoint clearly.
Write selectively.
Propose instead of writing when permissions, namespace, confidence, or visibility are unclear.
```
