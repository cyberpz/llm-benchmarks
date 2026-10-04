# Qwen3.8-27B-GSQ-RCO-IQ2_XS

## Benchmarks

| Epoch | Date | Backend | Gen tok/s | Prompt tok/s | TTFT s | Size GB | Config |
|---|---|---|---|---|---|---|---|
| E3 | 2026-09-26 | CUDA | 22.2 | 233.7 | - | 7.84 | np4 fit-auto spec-decode flash-attn |
| E3-tuned | 2026-09-26 | CUDA | 13.8 | - | - | - | np1 full-VRAM no-fit |
| E1 | 2026-09-18 | Vulkan/LMStudio | 17.3 | 51.1 | 1.06 | - | ngl999 Vulkan0 |

## Note

- Thinking mode: risposte in reasoning_content, TTFT alto
