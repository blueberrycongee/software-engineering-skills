# Benchmark Harness Guidance

Focus on reproducible measurements and clear reporting.

## Metrics
- tokens/s (decode only)
- GB/s for K/V reads and writes
- latency (p50/p90 or mean) per decode step

## Reproducibility
- Fix seeds for any randomized input.
- Record batch size, sequence length, block size, num_heads, head_dim, dtype, layout.
- Warm up (e.g., 10-20 iterations) before timing.

## Timing
- Use CUDA events for kernel timing.
- Report both per-iteration and aggregate stats.

## Reporting
- Include hardware and software details: GPU model, driver, CUDA version, WSL distro.
- Emit a machine-readable table (CSV or JSON) plus a short human summary.

## Baselines
- If comparing vLLM/TGI/TensorRT-LLM, record commit hash and configuration.
- Use identical shapes and layouts across baselines whenever possible.
