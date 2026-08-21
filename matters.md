# Matters: Design Rationale for Kemory Continuity Protocol

This document explains the important design choices behind the `kemory-continuity-protocol` skill.

Use it as implementation context for humans, maintainers, and platform-adapter authors. The canonical agent-facing instructions live in `SKILL.md`.

---

## 1. The goal is continuity, not memory dumping

The desired outcome is:

> Start a session in one agent, continue in the same agent later, or continue in a different agent without losing working state.

That means the skill must preserve more than isolated facts. It must preserve **session state**:

- objective;
- current state;
- decisions;
- constraints;
- open loops;
- artifacts;
- recommended next action;
- provenance;
- uncertainties.

This is why the protocol requires handoff checkpoints.

---

## 2. Same-agent and cross-agent continuation use the same mechanism

There should not be separate protocols for:

- “continue in Manus tomorrow”; and
- “continue in Claude from what Manus did.”

Both should use the same durable `continuity_session_id` in Kemory.

Platform-local chat IDs are useful metadata, but they are not durable continuity IDs.

Recommended distinction:

```text
continuity_session_id          Durable Kemory session/thread identity
source_platform_session_id     Local platform chat/session identity
```

---

## 3. Behavior is permission-derived, not platform-derived

Do not encode rules such as:

```text
Manus = read-only
Claude = read/write
Perplexity = candidate-only
```

Those may be good deployment defaults in some environments, but they are not universal truths.

Correct principle:

```text
agent identity + namespace + resource + action + visibility + org/team context = effective permission
```

The same platform can have different permissions for different agents or namespaces.

Therefore, adapters should describe mechanics only. They must not define authority.

---

## 4. Namespace hints are mandatory whenever possible

Kemory’s cross-agent value depends on different agents writing and reading the same project/customer/topic namespace.

Use namespaces for what the memory is about:

```text
project:kemory
project:kanvas
gtm:kemory
customer:eversheds
user:preferences
```

Do not fragment ordinary project memory by platform:

```text
manus:kemory       # avoid
claude:kemory      # avoid
chatgpt:kemory     # avoid
```

Store platform identity as metadata instead.

---

## 5. Checkpoints are first-class continuity objects

Generic memory captures durable facts. Checkpoints capture where work stands.

A memory might say:

```text
Kemory should use namespace hints for AI chat classification.
```

A checkpoint says:

```text
We are designing the cross-agent Kemory skill. We decided to use one canonical protocol, platform adapters for mechanics, permission-derived behavior, mandatory namespace hints, and checkpoint-before-exit. Next step: implement the skill package and upload it to GitHub.
```

The checkpoint is what lets another session continue without reconstructing the conversation.

---

## 6. Read-only agents still participate

Read-only agents should not be excluded from the continuity loop.

They can:

- retrieve context;
- do useful work;
- produce handoff checkpoint candidates;
- produce memory candidates;
- preserve provenance.

They must not:

- write/update/delete memory;
- promote document chunks;
- merge namespaces;
- broaden visibility.

This allows broad deployment without giving every agent write access.

---

## 7. Write access should still be selective

Even when write access exists, the agent should not write everything.

Write only content that is:

- durable;
- useful to future sessions or agents;
- scoped to a clear namespace;
- safe at the selected visibility;
- supported by provenance;
- not a duplicate of an existing memory.

Use expiry for temporary facts.

---

## 8. Uploaded documents are artifacts first

Do not store entire uploaded documents as ordinary memories by default.

Preferred flow:

```text
document upload -> artifact -> chunks/search -> cited use -> durable findings -> memory/checkpoint
```

This keeps memory clean while preserving access to source material.

---

## 9. Platform adapters should remain thin

Adapter files should answer mechanical questions:

- How does this platform load a skill?
- Can it call MCP directly?
- Can it use REST?
- Can it pass metadata fields such as `namespace_hint`?
- How does it expose uploaded files?
- What fallback should it use when tools are unavailable?

Adapters should not duplicate the full protocol. If the protocol changes, update `SKILL.md` first.

---

## 10. Recommended repository evolution

Suggested future additions:

```text
schemas/
  checkpoint.schema.json
  memory-candidate.schema.json
  session-start.schema.json

scripts/
  validate_checkpoint.py
  validate_memory_candidate.py

adapters/
  <platform-specific implementation notes>
```

Keep the canonical policy in `SKILL.md`. Keep rationale in this file. Keep runnable validators or helpers in `scripts/` if needed later.
