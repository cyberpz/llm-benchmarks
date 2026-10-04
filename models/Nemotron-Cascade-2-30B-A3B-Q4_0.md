# Nemotron-Cascade-2-30B-A3B-Q4_0

## Benchmarks

| Epoch | Date | Backend | Gen tok/s | Prompt tok/s | TTFT s | Size GB | Config |
|---|---|---|---|---|---|---|---|
| E3 | 2026-09-26 | CUDA | 90.5 | 459.0 | - | 17.02 | np4 fit-auto spec-decode flash-attn |
| E3-tuned | 2026-09-26 | CUDA | 69.3 | - | - | - | np1 full-VRAM no-fit |
| E2 | 2026-09-11 | CUDA/selector | 91.7 | - | - | - | np1 fit-off |
