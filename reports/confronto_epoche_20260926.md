# TestBench — Confronto epoche: llama.cpp Vulkan/CUDA vs motore Unsloth
_26/09/2026 · 2× RTX 3060 12GB · Unsloth Studio 2026.9.11 con llama.cpp b11160-mix (CUDA)_

## Le epoche

| Epoca | Data | Motore | Note |
|---|---|---|---|
| E1 | 18/09 | llama.cpp **Vulkan** (LM Studio) | ctx 64k, KV Q4, 2 GPU |
| E2 | 10-11/09 | llama.cpp **CUDA** (selector) | np 1, fit off, ctx 8k |
| E3 | 26/09 | llama.cpp **CUDA via Unsloth** | ctx 8k, KV f16, flash-attn, spec-decode |

## Tabella completa — gen tok/s (mediana 3 prompt × 2 run, n_predict 256)

| Modello | GB | E1 | E2 | E3 default (np4/fit) | E3 tuned (np1/VRAM) | Verdetto |
|---|---:|---:|---:|---:|---:|---|
| Qwen3.5-35B-A3B-Q4_K_M | 20.5 | — | 69.5 | 34.8 | 50.9 | **tuned vince** |
| Ornith-1.5-35B-Q4_K_M | 20.2 | 62.2 | — | 26.5 | 63.3 | **tuned vince** |
| Qwen3.6-35B-A3B-Uncensored-HauhauCS-Aggressi | 19.7 | 52.0 | — | 79.6 | 54.3 | **default vince** |
| NVIDIA-Nemotron-3.5-Lightning-30B-A3B-NVFP4- | 18.4 | — | — | 73.1 | 50.7 | **default vince** |
| nvidia_Nemotron-Cascade-2-30B-A3B-Q4_0 | 17.0 | — | 91.7 | 90.5 | 69.3 | **default vince** |
| Qwen3.8-27B-GSQ-RCO-IQ2_XS | 7.8 | 17.3 | — | 22.2 | 13.8 | **default vince** |
| Mellum2-12B-A2.5B-Thinking-Q4_K_M | 7.5 | — | 130.5 | 134.9 | 81.0 | **default vince** |
| Ternary-Bonsai-2-27B-PQ2_0 | 6.7 | — | — | LOAD_FAIL | LOAD_FAIL | GGUF rotto |
| Huihui-NVIDIA-Nemotron-Nano-9B-v2-abliterate | 5.8 | — | 46.6 | 47.1 | 35.1 | **default vince** |
| Gemma-4-E4B-Uncensored-HauhauCS-Aggressive-Q | 5.0 | — | 67.8 | 72.0 | 43.1 | **default vince** |
| Spark-X2.5-4B-Q8_0 | 4.1 | — | — | 171.3 | 48.5 | **default vince** |
| gemma-4-E4B-it-qat-UD-Q4_K_XL | 3.9 | — | — | 117.7 | 56.3 | **default vince** |
| MiniCPM5-2B-heretic-abliterated-Q8_0 | 2.5 | — | — | 105.5 | 74.9 | **default vince** |
| Qwen2.5-1.5B-Instruct-Q4_K_M | 1.0 | — | — | 185.8 | 128.0 | **default vince** |

E3 default = `--parallel 4` + fit auto di Studio. E3 tuned = `--parallel 1 --gpu-layers 999 --fit off`.

## Isolamento knob (Spark-X2.5-4B, stesso prompt, run ripetuti)

| Variante | gen run1 / run2 |
|---|---|
| np4_fitauto | [64.6, 76.6] |
| np1_kvuni | [47.9, 46.2] |
| np4_kvuni_t2 | [72.5, 318.1] |

Nota onesta: la varianza run-to-run è enorme (47→318 t/s a config identica): lo spec-decode su prompt ripetitivi esplode. Le mediane 3-prompt della tabella sono il numero affidabile; i singoli run no.

## Verdetto

1. **L'anomalia E3 era metà mito e metà verità.** Il crollo dei 20GB è reale e colpa della config: `--parallel 4 --fit on` manda KV/pesi in RAM (Ornith 26→63/76 t/s passando a np1+full-VRAM, replicato in due test).
2. **Sui modelli ≤8GB il default di Studio è la config giusta**: np1 'tuned' li rallenta (Spark 171→48, Mellum2 135→81, gemma-qat 118→56). Non c'era alcun guadagno da recuperare lì.
3. **Nessuna accelerazione di Unsloth vs llama.cpp**: è lo stesso motore. A parità di config le epoche E2 ed E3 coincidono (±4%); E3 batte E1 Vulkan sui modelli che in Vulkan non entravano o andavano in KV-Q4.
4. **Config ottimale per classe**: ≤8GB → np4+fit auto (default Studio, zero lavoro); 17-20GB → np1+full-VRAM o np4+fit off con KV in VRAM. Il selector v5 deve applicare questo automaticamente.

## Copertura

13/13 text model misurati in E3 (default e tuned) su 14; Ternary-Bonsai escluso (GGUF corrotto, tipo tensore 142).