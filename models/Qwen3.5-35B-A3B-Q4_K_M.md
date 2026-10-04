# Qwen3.5-35B-A3B-Q4_K_M

## Benchmarks

| Epoch | Date | Backend | Gen tok/s | Prompt tok/s | TTFT s | Size GB | Config |
|---|---|---|---|---|---|---|---|
| E3 | 2026-09-26 | CUDA | 34.8 | 155.9 | - | 20.50 | np4 fit-auto spec-decode flash-attn |
| E3-tuned | 2026-09-26 | CUDA | 50.9 | - | - | - | np1 full-VRAM no-fit |
| E2 | 2026-09-11 | CUDA/selector | 69.5 | - | - | - | np1 fit-off |

## Note

- Produzione [AGENT] / [CLIENT]
