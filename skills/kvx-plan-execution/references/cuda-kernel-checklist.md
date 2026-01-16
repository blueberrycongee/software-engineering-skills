# CUDA Kernel Skeleton Checklist (sm_86)

Use this when implementing the KVX single-step decode read/write kernel.

## Inputs and Metadata
- Validate layout enum and stride fields before kernel launch.
- Confirm block/page size and head_dim alignment.
- Treat invalid slots (e.g., -1) as no-op.

## Indexing and Layout
- Compute global slot = block_id * block_size + block_offset.
- Handle NHD vs HND (or other enums) with explicit stride math.
- Keep K and V indexing symmetric where possible.

## Memory and Alignment
- Ensure K/V base pointers are aligned to at least 16 bytes when using vectorized loads.
- Avoid uncoalesced access; prefer contiguous head or block dimensions.
- Consider `__half2` or vector types only when alignment allows.

## Kernel Structure
- Split read and write paths clearly; keep a simple, correct baseline.
- Add bounds checks early to avoid OOB writes.
- Use `__forceinline__` helpers for layout math to keep code readable.

## Performance Baseline
- Target correctness first; annotate hotspots for future tuning.
- Avoid unnecessary shared memory in the initial skeleton.
- Measure with small and large batch sizes to catch divergence.

## Build Notes
- Compile with `-arch=sm_86` for RTX 3060 Laptop.
- Run in WSL when possible; confirm GPU visibility via `nvidia-smi`.
