# KVX C ABI Guidelines

Use this checklist when adding or modifying the C ABI.

## Core Rules
- Use fixed-size integer types (`uint32_t`, `uint64_t`, `int32_t`) only.
- All public structs start with a `uint32_t size` field so callers can pass smaller structs safely.
- Add new fields only at the end of structs; never reorder or remove fields.
- Avoid `#pragma pack`; rely on natural alignment and document required alignment explicitly.
- Provide `extern "C"` guards and an export macro (e.g., `KVX_API`).

## Versioning
- Bump ABI major for breaking changes; bump minor for additive changes.
- Expose `kvx_get_version` returning ABI major/minor and struct size.
- Gate optional features behind capability flags or version checks.

## Error Handling
- Return status codes, not errno; keep codes stable.
- Include a helper to map status codes to strings if needed.

## Alignment and Layout
- Define alignment macros for buffers and structs.
- Document alignment requirements for K/V blocks and page tables.

## Test Expectations
- Add or update tests in `kvx/tests` to validate:
  - `kvx_get_version` consistency
  - `sizeof` and `alignof` expectations
  - Error code invariants

## Suggested Function Surface (minimal)
- `kvx_get_version`
- `kvx_create_cache` / `kvx_destroy_cache`
- `kvx_read` / `kvx_write` for single-step decode
- Optional: `kvx_get_capabilities`
