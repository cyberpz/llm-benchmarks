# Qwen3.6-35B-A3B-Uncensored-Q4_K_M

## Benchmarks

| Epoch | Date | Backend | Gen tok/s | Prompt tok/s | TTFT s | Size GB | Config |
|---|---|---|---|---|---|---|---|
| E3 | 2026-09-26 | CUDA | 79.6 | 499.3 | - | 19.71 | np4 fit-auto spec-decode flash-attn |
| E3-tuned | 2026-09-26 | CUDA | 54.3 | - | - | - | np1 full-VRAM no-fit |
| E1 | 2026-09-18 | Vulkan/LMStudio | 52.0 | - | - | - | ngl999 Vulkan0 |

## Note

- E1: crash ctx>4k su Vulkan; E3 OK CUDA
