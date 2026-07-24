---
name: Chat completion with structured JSON output
description: Force a Cerebras model to return JSON matching a schema using response_format.
api: openapi/cerebras-systems-chat-completions-openapi.yaml
operations:
  - createChatCompletion
---

# Chat completion with structured outputs (Cerebras Inference)

## Auth
- `Authorization: Bearer $CEREBRAS_API_KEY`; base URL `https://api.cerebras.ai`.

## Steps
1. Call `createChatCompletion` (`POST /v1/chat/completions`) with:
   - `model`, `messages`
   - `response_format`: `{ "type": "json_schema", "json_schema": { "name": ..., "schema": { ... } } }` to enforce a JSON Schema (or legacy `{ "type": "json_object" }`).
2. Parse `choices[0].message.content` as JSON — it conforms to the supplied schema.

## Rules
- Under API version 2, every nesting level of the schema must set `additionalProperties: false` (see `conventions/cerebras-systems-conventions.yml` and `lifecycle/cerebras-systems-lifecycle.yml`).
- Validate the parsed object against your schema before use; on `422`/`400` review the schema and request body.
- Docs: https://inference-docs.cerebras.ai/capabilities/structured-outputs
