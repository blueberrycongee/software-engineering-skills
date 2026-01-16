---
name: llmux-provider-alignment
description: Align LLMux providers with LiteLLM feature parity. Use when auditing provider gaps, mapping request/response/streaming behavior, or updating docs/LITELLM_PROVIDER_CHECKLIST.md while implementing provider fixes.
---

# LLMux Provider Alignment

## Overview

Align LLMux provider behavior with LiteLLM by applying a shared specification and
then fixing provider-specific gaps with tests, lint, and CI runs.

## Core Workflow

1) Identify the LiteLLM reference modules for the provider (transformation, common_utils, tests).
2) Fill a ProviderSpec for the provider (modes, stream protocol, model naming, API versioning, params).
3) Compare LLMux implementation against the ProviderSpec and record gaps.
4) Classify gaps as high-level (shared abstraction) or provider-specific (endpoint or schema details).
5) Implement fixes using TDD, include edge cases, run WSL `make check`, commit, and push.
6) Mark status in docs/LITELLM_PROVIDER_CHECKLIST.md.

## High-Level Gaps to Watch

- Missing capabilities matrix (supported params by provider and mode).
- No shared modal transformers (chat/embeddings/images/audio/responses/realtime).
- No stream protocol adapter (SSE vs JSON array vs eventstream).
- Inconsistent error mapping and usage parsing.
- Weak model naming and routing conventions (provider/model).

## Provider-Specific Gaps to Watch

- API versioning, endpoint paths, and authentication format.
- Parameter name mapping and default handling.
- Multimodal part conversion (image/audio/file/tool call).
- Streaming event format and partial JSON handling.
- Error schema quirks and status-to-error mapping.

## ProviderSpec Template

```yaml
ProviderSpec:
  name: "gemini"
  modes: [chat, embeddings, images, audio, responses, realtime, batch]
  stream_protocol: sse
  model_naming: "gemini/<model>"
  api_versions: { default: v1beta, overrides: [v1alpha for gemini-3] }
  parameters:
    supported:
      chat: [temperature, top_p, top_k, max_tokens, stop, tools, tool_choice, response_format, n, thinking]
      embeddings: [input, model]
    unsupported: [logprobs, frequency_penalty]
  request_mapping:
    - map max_tokens -> maxOutputTokens
    - map response_format -> response_mime_type/response_schema
    - map tools -> function_declarations
  response_mapping:
    - map finish_reason
    - map usage
  error_mapping:
    - map status -> LLMError class
```

## References

Read `references/provider-alignment-spec.md` for the detailed alignment spec.
