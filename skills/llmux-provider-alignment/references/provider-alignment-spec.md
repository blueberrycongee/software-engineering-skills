# LLMux Provider Alignment Spec (Draft v0.1)

## Purpose

Define a repeatable, high-confidence workflow to align LLMux providers with
LiteLLM behavior and features, while avoiding toy or fake implementations.

## Scope

- Applies to all LLMux providers and transport adapters.
- Focus on code paths and runtime behavior, not docs or comments.
- Target: enterprise-grade LLM gateway (microservice/monolith dual-mode).

## Definitions

- High-level gaps: issues that should be solved once and shared (capability
  matrix, streaming adapters, modality transforms, error mapping, usage).
- Provider-specific gaps: issues tied to an API's schema, auth, or endpoints.

## Workflow (Repeatable)

1) Identify LiteLLM reference modules for the provider (transformation,
   common_utils, error handling, tests).
2) Fill a ProviderSpec for the provider (modes, stream protocol, model naming,
   API versioning, parameter support).
3) Compare LLMux implementation with ProviderSpec and record gaps.
4) Classify gaps into high-level vs provider-specific.
5) Fix with TDD, run WSL lint/CI, and update docs/LITELLM_PROVIDER_CHECKLIST.md.

## Capabilities Matrix (High-Level)

For each provider and mode, record whether these are supported, rejected, or
ignored with warnings:

- chat: temperature, top_p, top_k, max_tokens, stop, tools, tool_choice,
  response_format, response_schema, n, thinking, logprobs, seed, presence,
  frequency
- embeddings: input, model, dimensions, encoding_format
- audio: input, voice, format, timestamp_granularities
- images: size, quality, style, background, response_format
- batch/realtime/responses: request_id, stream control, polling/async

## Modality Transforms (High-Level)

Create shared transforms so providers only handle deltas:

- chat: tool call serialization, system/user separation, function schema
- embeddings: vector size and normalization differences
- images/audio: part handling, media types, base64/file_uri
- responses/realtime: event stream mapping and output aggregation

## Streaming Protocols (High-Level)

Define a StreamAdapter with protocol types:

- sse: "data:" chunks, event type handling, JSON per event
- json_array: chunked array elements, partial JSON tolerance
- eventstream: custom event envelopes

Providers declare protocol and rely on shared parsing utilities.

## Errors and Usage (High-Level)

Error mapping must convert provider errors into a stable LLMux error taxonomy:

- auth, rate_limit, invalid_request, server_error, timeout, unavailable

Usage mapping must consistently fill tokens and cost fields when available.

## Model Naming and Routing (High-Level)

Use a consistent provider/model format, with explicit aliases. Avoid ad-hoc
SupportsModel logic scattered across code.

## Provider-Specific Gaps

Address per-provider differences:

- API versions, endpoints, authentication (headers, query keys, SigV4)
- payload schema (tools, response_format, thinking, safety settings)
- streaming event formats and flush semantics
- multimodal content parts and attachments

## Acceptance Criteria

- Parity with LiteLLM behavior for each supported parameter and mode.
- No silent drops of fields without explicit warnings or capability flags.
- Streaming output consistent with provider protocol (no fake parsing).
- Errors are mapped and surfaced with actionable metadata.

## ProviderSpec Template (Example)

```yaml
ProviderSpec:
  name: "gemini"
  modes: [chat, embeddings, images, audio, responses, realtime, batch]
  stream_protocol: sse
  model_naming: "gemini/<model>"
  api_versions: { default: v1beta, overrides: [v1alpha for gemini-3] }
  parameters:
    supported:
      chat: [temperature, top_p, top_k, max_tokens, stop, tools, tool_choice,
             response_format, response_schema, n, thinking]
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

