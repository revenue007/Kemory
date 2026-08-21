# Platform Adapters

Adapters describe platform mechanics for using the Kemory Continuity Protocol.

They must not redefine the core memory policy. The canonical policy is `../SKILL.md`.

## Adapter rules

Adapters may specify:

- how to load the skill;
- how to call Kemory MCP or REST;
- how to pass `namespace_hint`;
- how to pass `continuity_session_id`;
- how to expose uploaded files/artifacts;
- how to format checkpoint or memory-candidate fallback output;
- what to do when tools are unavailable.

Adapters must not specify hard-coded authority such as:

```text
Manus is read-only.
Claude can write.
Perplexity cannot write.
```

Authority must come from effective Kemory permissions for the specific agent identity, namespace, resource, and action.

## Recommended adapter structure

```text
# <Platform> Adapter

## Purpose

## Loading the skill

## Calling Kemory

## Required metadata

## Upload/artifact handling

## Permission handling

## Fallback behavior
```
