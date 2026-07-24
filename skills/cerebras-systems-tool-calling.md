---
name: Chat completion with tool calling
description: Let a Cerebras model call your functions by passing tool definitions and executing the returned tool_calls.
api: openapi/cerebras-systems-chat-completions-openapi.yaml
operations:
  - createChatCompletion
---

# Chat completion with tool calling (Cerebras Inference)

## Auth
- `Authorization: Bearer $CEREBRAS_API_KEY`; base URL `https://api.cerebras.ai`.

## Steps
1. Call `createChatCompletion` (`POST /v1/chat/completions`) with:
   - `model`, `messages`
   - `tools`: array of function definitions (name, description, JSON-schema parameters)
   - `tool_choice`: `"auto"`, `"required"`, `"none"`, or a specific function object.
2. If `choices[0].finish_reason == "tool_calls"`, read `choices[0].message.tool_calls`; execute each function with the JSON `arguments`.
3. Append a `{role: "tool", tool_call_id, content}` message with each result and call `createChatCompletion` again to get the final answer.

## Rules
- Keep the full running `messages` history across turns.
- API version 2 (default 2026-07-21) enforces stricter tool-call message validation and `additionalProperties: false` on schemas — see `lifecycle/cerebras-systems-lifecycle.yml`.
- Handle `400 BadRequestError` for malformed tool schemas; `429` → exponential backoff.
