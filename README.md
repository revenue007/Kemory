# Kemory — Persistent Memory Instructions

Kemory is the persistent, permission-aware memory layer for this project. Use its `kemory_*` MCP tools proactively so that useful context survives across sessions.

## Session Startup

At the beginning of a new session or substantial task:

- Use `kemory_list_namespaces` to understand the available memory structure when it is not already known.
- Use `kemory_get_user_context` to load relevant user preferences and cross-project context.
- Use `kemory_recall_memory` for the current project, feature, task, customer, or topic before making plans or assumptions.
- If the task concerns an existing decision, implementation, requirement, bug, person, or prior discussion, search Kemory before answering.
- Treat recalled memories as context, not unquestionable truth. Reconcile them with the repository, current user instructions, and more recent evidence.
- Do not wait for the user to explicitly say "check memory" when prior context could materially improve the answer.

## Choosing the Right Retrieval Tool

| Tool | Use when… |
|---|---|
| `kemory_recall_memory` | Default search across namespaces |
| `kemory_get_context` | A synthesized overview of a topic is more useful than individual results |
| `kemory_find_similar` | Looking for semantically related material that may use different wording |
| `kemory_get_raw` | Exact source text, wording, code, requirements, or unsummarized evidence is needed |
| `kemory_get_compressed` | The same material is needed at raw, key-fact, or concept-synthesis levels |
| `kemory_get_history` | Provenance, changes, authorship, or the evolution of a memory matters |
| `kemory_get_session_context` | Reviewing what the current session has already read or written |
| `kemory_rehydrate_session_sources` | Source material behind earlier session memories is required |

Prefer targeted retrieval over loading large amounts of unrelated memory.

## When to Write Memory

Use `kemory_store_memory` when information is durable and likely to improve future work, including:

- Explicit user preferences
- Project goals and stable requirements
- Architecture and implementation decisions
- Important constraints and non-obvious conventions
- Decisions with their reasoning and trade-offs
- Confirmed facts about customers, products, systems, or workflows
- Resolved incidents and reusable lessons
- Important corrections to earlier assumptions
- Open questions or commitments that must survive the session

A useful memory should normally capture:

- What was decided or learned
- Why it matters
- Relevant context or reasoning
- Date or temporal context when important
- Project, customer, repository, or topic
- Source or provenance when available
- Whether it is confirmed, tentative, superseded, or time-sensitive

Store concise, self-contained memories. Preserve enough context that a future session can understand them without reading the current conversation.

## What Not to Store

Do not store:

- Passwords, private keys, API keys, access tokens, cookies, or other credentials
- Temporary command output or routine progress updates
- Speculation presented as fact
- Large duplicated passages when a concise durable memory is sufficient
- Information that is already obvious and authoritative in the repository
- Sensitive personal information unless the user explicitly requests it and storage is appropriate

> **Never** place secrets in Kemory, source control, `CLAUDE.md`, logs, or generated documentation.

## Namespace Conventions

Prefer clear, stable namespaces such as:

- `user:preferences`
- `project:<project-name>`
- `decisions:<project-name>`
- `customer:<customer-name>`
- `product:<product-name>`
- `tribal:<team-or-domain>`
- `research:<topic>`

Use the narrowest appropriate namespace by default. Do not assume that a wider namespace is safe.

Memories are private to the user unless explicitly shared or promoted. Before using `kemory_promote_memory`, use `kemory_check_access` when visibility is uncertain and explain the intended wider audience.

## Updating and Correcting Memory

`kemory_store_memory` does **not** silently overwrite existing memories.

When new information changes an earlier conclusion:

1. Retrieve the relevant existing memory.
2. Record the correction or superseding decision with clear status and date.
3. Preserve the reason for the change.
4. Use `kemory_get_history` when the evolution of the information matters.

Do not quietly treat an outdated memory as current.

## Destructive and Sharing Actions

Ask for explicit confirmation before:

- `kemory_delete_memory`
- Promoting private or narrowly scoped memory into a wider/shared namespace
- Storing unusually sensitive information

Use `kemory_check_access` before an uncertain read, write, delete, or promotion. Never infer permissions from namespace names alone.

## End-of-Task Behavior

At the end of a substantial task:

- Identify durable decisions, preferences, constraints, corrections, and unresolved questions.
- Store only the items that will improve future sessions.
- Use `kemory_consolidate_session` when the session contains multiple durable outcomes worth preserving.
- Avoid filling Kemory with conversational summaries that have no future value.
- Briefly mention meaningful memories written, but do not produce a long memory-operation log unless requested.

## Output Quality Rules

When using Kemory context:

- Distinguish confirmed facts from tentative memories.
- Prefer current repository evidence and direct user instructions over older memories.
- Mention conflicts or stale information instead of hiding them.
- Preserve provenance for consequential claims.
- Do not invent a memory when retrieval returns nothing.
- If evidence is insufficient, say so and ask a focused question.
- Use memory to reduce repeated questions, not to override the user.
