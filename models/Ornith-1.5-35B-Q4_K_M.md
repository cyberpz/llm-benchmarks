# Ornith-1.5-35B-Q4_K_M

## Benchmarks

| Epoch | Date | Backend | Gen tok/s | Prompt tok/s | TTFT s | Size GB | Config |
|---|---|---|---|---|---|---|---|
| E3 | 2026-09-26 | CUDA | 26.5 | 145.9 | - | 20.22 | np4 fit-auto spec-decode flash-attn |
| E3-tuned | 2026-09-26 | CUDA | 63.3 | - | - | - | np1 full-VRAM no-fit |
| E1 | 2026-09-18 | Vulkan/LMStudio | 62.2 | - | - | - | ngl999 Vulkan0 |

## Note

- E1: non testato Vulkan (troppo grande); E3 tuned 2.4x vs default
