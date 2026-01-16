---
name: kvx-plan-execution
description: Plan and implement the KVX GPU-first KV cache standard in d:\Desktop\kvx-workspace, including comparing vLLM/TensorRT-LLM/TGI layouts, drafting KVX v1 spec and C ABI, writing CUDA kernel skeletons, and building reproducible benchmarks on Windows+WSL with an RTX 3060 Laptop GPU. Use when requests mention KVX, KV cache standardization, paged KV layouts, KVX spec/ABI/kernels/benchmarks, or updates to kvx/PLAN.md or kvx/KV_CACHE_LAYOUTS.md.
---

# Kvx Plan Execution

## Overview

Deliver the KVX v1 plan end-to-end: layout comparison, spec drafting, C ABI definition, CUDA kernel skeletons, and benchmark harness work.
Optimize for correctness, reproducibility, and RTX 3060 Laptop (sm_86) performance using Windows + WSL workflows.

## Workflow

- **Step 0: Load project context.**
  - Read `kvx/PLAN.md` and `kvx/KV_CACHE_LAYOUTS.md`.
  - Identify which plan step(s) the user wants and map outputs to concrete files.
  - Use `rg` against local clones (`vllm`, `TensorRT-LLM`, `text-generation-inference`) when more details are needed.

- **Step 1: Layout comparison and adapter mapping.**
  - Update `kvx/KV_CACHE_LAYOUTS.md` with authoritative citations and precise shapes/metadata.
  - Call out shared fields, layout enums, and adapter conversion points.

- **Step 2: KVX v1 spec drafting.**
  - Use `references/spec-template.md` for section order and normative language.
  - Ensure layout, page table, metadata, API semantics, error codes, and version negotiation are defined.

- **Step 3: C ABI definition.**
  - Use `references/abi-guidelines.md` to keep ABI stable (size fields, alignment, reserved fields).
  - Keep `kvx/tests/kvx_abi_test.c` passing and add tests for any new ABI surface.

- **Step 4: CUDA kernel skeletons.**
  - Use `references/cuda-kernel-checklist.md` for layout handling and sm_86 constraints.
  - Focus on correctness, memory layout clarity, and easy extension to optimized kernels.

- **Step 5: Benchmark harness and adapters.**
  - Use `references/benchmark-harness.md` for metrics and reproducibility.
  - Capture tokens/s, GB/s, latency, and report baseline comparisons.

## Windows + WSL Notes

- Prefer WSL for CUDA builds and benchmarks; use `wsl` to run commands from Windows.
- Target RTX 3060 Laptop (sm_86); compile kernels with `-arch=sm_86` or equivalent.
- Record GPU, driver, CUDA, and WSL distro details in benchmark outputs.

## Quality Gates

- Keep spec, ABI, and kernel code consistent; update docs and tests together.
- Avoid unstated assumptions; document any engine-specific adapter behavior.
- Provide reproducible benchmark settings (seed, batch sizes, sequence lengths, warm-up).

## References

- `references/spec-template.md` for KVX v1 spec structure and required sections.
- `references/abi-guidelines.md` for ABI stability, versioning, and alignment.
- `references/cuda-kernel-checklist.md` for kernel skeleton correctness on sm_86.
- `references/benchmark-harness.md` for metrics and reporting format.

## Resources (optional)

Create only the resource directories this skill actually needs. Delete this section if no resources are required.

### scripts/
Executable code (Python/Bash/etc.) that can be run directly to perform specific operations.

**Examples from other skills:**
- PDF skill: `fill_fillable_fields.py`, `extract_form_field_info.py` - utilities for PDF manipulation
- DOCX skill: `document.py`, `utilities.py` - Python modules for document processing

**Appropriate for:** Python scripts, shell scripts, or any executable code that performs automation, data processing, or specific operations.

**Note:** Scripts may be executed without loading into context, but can still be read by Codex for patching or environment adjustments.

### references/
Documentation and reference material intended to be loaded into context to inform Codex's process and thinking.

**Examples from other skills:**
- Product management: `communication.md`, `context_building.md` - detailed workflow guides
- BigQuery: API reference documentation and query examples
- Finance: Schema documentation, company policies

**Appropriate for:** In-depth documentation, API references, database schemas, comprehensive guides, or any detailed information that Codex should reference while working.

### assets/
Files not intended to be loaded into context, but rather used within the output Codex produces.

**Examples from other skills:**
- Brand styling: PowerPoint template files (.pptx), logo files
- Frontend builder: HTML/React boilerplate project directories
- Typography: Font files (.ttf, .woff2)

**Appropriate for:** Templates, boilerplate code, document templates, images, icons, fonts, or any files meant to be copied or used in the final output.

---

**Not every skill requires all three types of resources.**
