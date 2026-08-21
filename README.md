# Kemory Continuity Protocol

A portable AI-agent skill for using **Kemory** as a durable memory and continuity layer across:

- repeated sessions in the same agent;
- handoffs between different agents;
- project/customer/topic workflows;
- uploaded documents and artifacts;
- durable decisions, preferences, constraints, and open loops.

The goal is simple:

> Start work in one AI agent, continue in another, and preserve enough state that the next session does not restart from scratch.

This skill is intended for agents such as Manus, Claude, ChatGPT/OpenAI, Gemini, Perplexity, and other agents that can use Kemory through MCP, REST, or a platform-specific adapter.

---

## Repository contents

```text
.
├── SKILL.md                         # Canonical skill instructions
├── README.md                        # Overview and setup notes
├── matters.md                       # Design rationale and implementation matters
├── adapters/
│   ├── README.md                    # Adapter design rules
│   ├── manus.md                     # Manus adapter notes
│   ├── claude.md                    # Claude adapter notes
│   ├── openai-chatgpt.md            # OpenAI / ChatGPT adapter notes
│   ├── gemini.md                    # Gemini adapter notes
│   └── perplexity.md                # Perplexity adapter notes
└── examples/
    ├── handoff-checkpoint.md        # Reusable checkpoint template
    ├── memory-candidate.md          # Reusable memory candidate template
    └── session-start.md             # Session hydration template
```

---

## Core idea

The skill is not a generic “save memory” prompt. It is a **continuity protocol**.

Agents should follow this loop:

```text
hydrate → work → checkpoint → write/propose memory → handoff/resume
```

This supports both:

1. **Inter-session continuity, same agent**
   - Example: start in Manus today, continue in a new Manus session tomorrow.

2. **Inter-agent continuity**
   - Example: start in Manus, continue in Claude, then use ChatGPT to turn the result into GTM copy.

---

## Permission-derived behavior

The most important rule:

> Kemory behavior is permission-derived, not platform-derived.

Do not assume Manus is read-only, Claude is read/write, or Perplexity is candidate-only. The same platform can have different permissions depending on the agent identity, namespace, org, team, resource, or action.

At runtime, the agent should determine effective permissions for actions such as:

- memory read;
- memory write/update/delete;
- artifact read/write;
- checkpoint read/write;
- namespace move/merge;
- visibility broadening.

If write access is absent or uncertain, the agent should produce structured memory/checkpoint candidates instead of attempting writes.

---

## Recommended installation

### 1. Add the core skill

Use `SKILL.md` as the canonical skill body in the target agent or skill system.

Recommended skill name:

```text
kemory-continuity-protocol
```

Recommended description:

```text
Use when an AI agent needs Kemory for persistent memory, same-agent inter-session continuity, cross-agent handoff, session resume, uploaded document continuity, or durable memory writeback across Manus, Claude, ChatGPT/OpenAI, Gemini, Perplexity, and other agents. Behavior is permission-derived at runtime between the specific agent identity and Kemory; platform adapters define mechanics only.
```

### 2. Add platform adapters only if needed

The files in `adapters/` are intentionally thin. They should describe platform mechanics only:

- how to load the skill;
- how to call Kemory MCP or REST;
- how to pass `namespace_hint` and `continuity_session_id`;
- how to handle uploads;
- how to format fallback handoff output when tools are unavailable.

Adapters must not redefine memory policy or assume authority from platform identity.

### 3. Standardize namespace and session fields

Every agent should preserve these fields when possible:

```text
namespace_hint
continuity_session_id
source_platform
source_agent_id
source_platform_session_id
permissions_mode
task_objective
artifact_ids
visibility
created_at
expires_at / ttl_seconds
```

The most important are:

```text
namespace_hint
continuity_session_id
source_platform
```

---

## Operating model

### Start of session

1. Identify whether this is a new continuity session or a continuation.
2. Set or infer `namespace_hint`.
3. Set or infer `continuity_session_id`.
4. Determine effective Kemory permissions.
5. Retrieve authorized context.
6. Continue from the latest checkpoint, not from scratch.

### During session

1. Keep the same namespace and continuity session unless the task changes.
2. Track decisions, assumptions, constraints, sources, artifacts, and open loops.
3. Treat uploaded documents as artifacts first, not as giant memories.
4. Preserve provenance.

### End of session

1. Create or propose a handoff checkpoint.
2. Store durable memories only if write access exists.
3. Produce memory candidates if write access is absent or uncertain.
4. Include recommended next action.

---

## Examples

### Same-agent continuation

```text
Continue the Kemory skill work from yesterday.
```

Expected behavior:

```text
1. Infer likely namespace.
2. Find latest checkpoint.
3. Retrieve supporting context.
4. Briefly restate recovered state.
5. Continue work.
6. Save/propose updated checkpoint.
```

### Cross-agent continuation

```text
Continue in Claude what I started in Manus.
```

Expected behavior:

```text
1. Retrieve latest Manus checkpoint for the continuity session.
2. Retrieve supporting memories and artifacts.
3. Continue from current_state and recommended_next_action.
4. Save/propose a new checkpoint with Claude as source_platform.
```

### Read-only fallback

```text
Use Kemory context and draft the GTM page.
```

Expected behavior:

```text
1. Retrieve authorized Kemory context.
2. Draft the requested output.
3. Do not write memory if write access is unavailable.
4. Produce memory candidates and a checkpoint candidate.
```

---

## Non-goals

This skill should not be used to:

- bypass Kemory permissions;
- replace the gatekeeper model;
- merge namespaces automatically;
- store raw documents as ordinary memories;
- memorize every chat turn;
- use platform identity as the primary namespace;
- broaden visibility without explicit authorization.

---

## License

Choose the license that matches your intended distribution. If this is intended to be public/open-source, add a `LICENSE` file before publishing.
