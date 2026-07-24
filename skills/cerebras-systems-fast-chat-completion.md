---
name: Run a fast chat completion
description: Call the Cerebras Inference API to generate a low-latency chat response from a list of role-tagged messages.
api: openapi/cerebras-systems-chat-completions-openapi.yaml
operations:
  - createChatCompletion
  - list_models_v1_models_get
---

# Run a fast chat completion (Cerebras Inference)

Use this to get an ultra-fast LLM response from Cerebras.

## Auth
- Set `Authorization: Bearer $CEREBRAS_API_KEY`. Create keys at https://cloud.cerebras.ai.
- Base URL: `https://api.cerebras.ai` (OpenAI-compatible routes under `/v1`).

## Steps
1. (Optional) Discover models with `list_models_v1_models_get` (`GET /v1/models`) and pick a `model` id (e.g. `gpt-oss-120b`, `gemma-4-31b`, `zai-glm-4.7`).
2. Call `createChatCompletion` (`POST /v1/chat/completions`) with:
   - `model`: the chosen id
   - `messages`: array of `{role, content}` where role is one of `system`, `user`, `assistant`, `developer`, `tool`
   - optional: `stream: true` for SSE, `temperature`, `top_p`, `max_completion_tokens`.
3. Read `choices[0].message.content` for the answer; check `choices[0].finish_reason` (`stop`, `length`, `tool_calls`, `content_filter`). `usage` holds token counts.

## Rules
- On `429 RateLimitError`, back off exponentially; two token buckets exist (uncached vs total TPM) — see `rate-limits/cerebras-systems-rate-limits.yml`.
- Errors use `{ "error": { message, type, code, param } }`; see `errors/cerebras-systems-problem-types.yml`.
- No idempotency key — completions are non-idempotent, so do not blindly retry a succeeded request.
