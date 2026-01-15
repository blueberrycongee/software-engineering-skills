---
name: rust-best-practices
description: Rust development best practices and coding standards. Use when asked to write, enforce, or review Rust project structure, style, ownership/borrowing, error handling, async/concurrency, modularity/visibility, or testing/docs conventions; includes a concise self-checklist for Rust teams.
---

# Rust Best Practices (v1.0)

Follow this skill to draft or review Rust codebases against the provided规范. Keep guidance concise and actionable.

## Core Philosophy

- Treat the compiler as your code reviewer.
- If code feels awkward (e.g., excessive `.clone()`), re-check the design.

## Project Structure (Cargo & Workspaces)

- Use Cargo; do not hand-manage dependencies.
- For projects with more than 3 crates, use a Workspace (monorepo).
- Keep root `Cargo.toml` for members only; no dependencies there.
- Use a layered layout:
  - `crates/` for core business logic libraries.
  - `apps/` for executables (API server, CLI).
- Prefer shared `target/` to speed builds and keep dependency versions consistent.

### Directory Conventions

- `src/main.rs`: minimal binary entry; only startup/config.
- `src/lib.rs`: library entry; business logic lives here.
- `examples/`: runnable examples (`cargo run --example xxx`).
- `tests/`: integration tests for external-facing APIs.

## Idiomatic Rust Style

### Naming (enforce)

- variables/functions/modules: `snake_case`
- structs/enums/traits: `PascalCase`
- constants/statics: `SCREAMING_SNAKE_CASE`

### Formatting & Lint

- Require `cargo fmt` before commit; CI must enforce.
- Keep `cargo clippy` at zero warnings during development.
- Prefer clippy guidance for idiomatic patterns (e.g., `&[T]` over `&Vec<T>`).

## Ownership & Borrowing

- Avoid excessive `.clone()`; prefer borrowing or moving.
- If a field is a dense `'a` lifetime web, consider owning data or using `Arc` to decouple.

### Smart Pointer Selection

- `Box<T>` for heap allocation, recursion, or sized indirection.
- `Arc<T>` for multi-thread shared immutable data.
- `Rc<T>` for single-thread shared immutable data.
- `Mutex<T>`/`RwLock<T>` for interior mutability, usually with `Arc`.

## Error Handling

- Do not use `.unwrap()` in production code.
- Use `expect("reason")` only for truly impossible cases.
- Prefer `?` to propagate errors with context.

### Error Crate Choice

- Applications: use `anyhow` with `context(...)`.
- Libraries: use `thiserror` with explicit error enums.

## Async & Concurrency

- Prefer Tokio unless a strong reason exists.
- Do not block inside `async fn` (e.g., `std::thread::sleep` or CPU-heavy loops).
- Use `tokio::task::spawn_blocking` for blocking work.

## Modularity & Visibility

- Default to `pub(crate)`; do not make everything `pub`.
- Use `pub` only for stable public API surface.
- For large libraries, provide a `prelude` module for common exports.

## Testing & Docs

- Unit tests: inline in source under `mod tests` with `#[cfg(test)]`.
- Integration tests: in `tests/` and use only public APIs.
- Doc tests: keep `///` examples runnable; `cargo test` must pass them.

## Self-Check List

- `cargo clippy` clean?
- No `.unwrap()` left?
- `thiserror` for libraries and `anyhow` for applications?
- No blocking in async code?
- Public APIs documented with passing doc tests?
