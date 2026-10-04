# Mellum2-12B-A2.5B-Thinking-Q4_K_M

## Benchmarks

| Epoch | Date | Backend | Gen tok/s | Prompt tok/s | TTFT s | Size GB | Config |
|---|---|---|---|---|---|---|---|
| E3 | 2026-09-26 | CUDA | 134.9 | 995.6 | - | 7.52 | np4 fit-auto spec-decode flash-attn |
| E3-tuned | 2026-09-26 | CUDA | 81.0 | - | - | - | np1 full-VRAM no-fit |
| E2 | 2026-09-11 | CUDA/selector | 130.5 | - | - | - | np1 fit-off |

## Note

- Default router GPU1
