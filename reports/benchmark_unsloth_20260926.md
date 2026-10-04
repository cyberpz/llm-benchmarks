# Benchmark Unsloth Studio — TestBench

**Data:** 26/09/2026 16:49 · **Motore:** Unsloth Studio 2026.9.11 / llama.cpp CUDA (b11160-mix-a6922cc)
**Hardware:** 2× RTX 3060 12GB · i7-4770K · 32GB RAM
**Metodo:** load API Studio (`gpu_memory_mode=auto`), 3 prompt (codice/analisi/creatività) × 2 run, n_predict=256, ctx 8192. Gen Speed = mediana dei 6 run. Timings dall'endpoint nativo llama.cpp.

## Modelli text-generation

| Modello | Dim (GB) | Configurazione | Prompt Speed | Gen Speed | Status |
|---|---|---|---|---|---|
| Qwen2.5-1.5B-Instruct Q4_K_M | 1.04 | fit off, spec, fa, np4, ctx8192 | 3463.5 t/s | 185.8 t/s | ✅ OK |
| Spark-X2.5-4B Q8_0 | 4.07 | fit off, spec, fa, np4, ctx8192 | 1530.9 t/s | 171.3 t/s | ✅ OK |
| Mellum2-12B-A2.5B-Thinking Q4_K_M | 7.52 | fit off, spec, fa, np4, ctx8192 | 995.6 t/s | 134.9 t/s | ✅ OK |
| gemma-4-E4B-it-qat UD-Q4_K_XL | 3.93 | fit off, spec, fa, np4, ctx8192 | 1349.9 t/s | 117.7 t/s | ✅ OK |
| MiniCPM5-2B-heretic-abli Q8_0 | 2.50 | fit off, spec, fa, np4, ctx8192 | 2327.1 t/s | 105.5 t/s | ✅ OK |
| Nemotron-Cascade-2-30B-A3B Q4_0 | 17.02 | fit off, spec, fa, np4, ctx8192 | 459.0 t/s | 90.5 t/s | ✅ OK |
| Qwen3.6-35B-A3B Uncensored Q4_K_M | 19.71 | fit off, spec, fa, np4, ctx8192 | 499.3 t/s | 79.6 t/s | ✅ OK |
| Nemotron-3.5-Lightning-30B-A3B NVFP4 | 18.43 | fit off, spec, fa, np4, ctx8192 | 314.4 t/s | 73.1 t/s | ✅ OK |
| Gemma-4-E4B Uncensored Q4_K_M | 4.97 | fit off, spec, fa, np4, ctx8192 | 1268.2 t/s | 72.0 t/s | ✅ OK |
| Huihui-Nemotron-Nano-9B-v2-abli Q4_K_S | 5.79 | fit off, spec, fa, np4, ctx8192 | 734.4 t/s | 47.1 t/s | ✅ OK |
| Qwen3.5-35B-A3B Q4_K_M | 20.50 | fit auto, spec, fa, np4, ctx8192 | 155.9 t/s | 34.8 t/s | ✅ OK |
| Ornith-1.5-35B Q4_K_M | 20.22 | fit auto, spec, fa, np4, ctx8192 | 145.9 t/s | 26.5 t/s | ✅ OK |
| Qwen3.8-27B-GSQ-RCO IQ2_XS | 7.84 | fit off, spec, fa, np4, ctx8192 | 233.7 t/s | 22.2 t/s | ✅ OK |
| Ternary-Bonsai-2-27B PQ2_0 | 6.71 | ctx8192 | — | — | ❌ LOAD_FAIL |

## Altri modelli (OCR / audio / ASR / encoder / embed / diffusion)

| Modello | Dim (GB) | Configurazione | Prompt Speed | Gen Speed | Status |
|---|---|---|---|---|---|
| Unlimited-OCR BF16 | 5.47 | fit off, spec, fa, np4, ctx8192 | 2301.8 t/s | 544.4 t/s | ✅ OK |
| Qwen3-ASR-1.7B Q8_0 | 2.02 | fit off, spec, fa, np4, ctx8192 | 2882.1 t/s | 162.0 t/s | ✅ OK |
| LFM2.5-Audio-1.5B bf16 | 2.18 | fit off, spec, fa, np4, ctx8192 | 3278.9 t/s | 147.8 t/s | ✅ OK |
| flux2-klein-9b text-encoder Q6_K | 6.26 | fit off, spec, fa, np4, ctx8192 | 971.6 t/s | 48.8 t/s | ✅ OK |
| qwen-image-2.1-UC Q8_0 | 7.07 | ctx8192 | — | — | ❌ LOAD_FAIL |
| nomic-embed-text-v2-moe Q8_0 | 0.48 | fit off, spec, fa, np4, ctx8192 | — | — | ⚠️ NO_OUTPUT |

## Note
- `Gen Speed` = mediana; picchi anomali (best) esclusi dalla colonna principale.
- `fit auto` = llama.cpp adatta n_gpu_layers alla VRAM libera; per i 30B+ il KV può finire in RAM.
- Ternary-Bonsai PQ2_0: GGUF non caricabile (tipo tensore 142 fuori range) — incompatibile con questa build.
- qwen-image-2.1: diffusion, non è un modello chat; va usato via pipeline image, non llama.cpp.
- nomic-embed: embeddings, nessun output di testo (atteso).