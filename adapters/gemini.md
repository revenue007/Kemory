# Gemini Adapter

Use this adapter for Gemini-specific mechanics. Follow `../SKILL.md` for core policy.

## Purpose

Use Gemini for multimodal, Google-workspace, document-heavy, or broad synthesis workflows that need Kemory continuity.

## Loading the skill

Load or paste the Kemory Continuity Protocol at the start of relevant sessions or include a bootstrap instruction in the agent configuration.

## Required metadata

```text
source_platform: Gemini
source_agent_id: <agent identity>
source_platform_session_id: <session id if available>
namespace_hint: <project/customer/topic namespace>
continuity_session_id: <durable Kemory session id>
```

## Permission handling

Do not assume Gemini can read or write. Determine effective Kemory permissions at runtime.

If direct artifact upload/write is unavailable, produce artifact-registration and memory candidates instead of writing.

## Upload/artifact handling

Preserve source metadata for multimodal or document inputs. Prefer artifact registration and chunk-level use over whole-document memory writes.
