# LLM Inference Benchmarks — TestBench

Benchmark di velocità inferenza LLM su **TestBench** (2× NVIDIA RTX 3060 12GB, Windows 11, llama.cpp CUDA).

## Hardware

| Componente | Specifica |
|---|---|
| GPU | 2× NVIDIA RTX 3060 12GB (CUDA) |
| CPU | AMD Ryzen 7 5800X |
| RAM | 64GB DDR4-3600 |
| OS | Windows 11 Pro |
| Backend | llama.cpp (CUDA), LM Studio |

## Struttura

```
reports/          # Benchmark completi (MD + HTML)
LLMs/             # Report modelli locali e selettore CUDA
```

## Report disponibili

### reports/

| File | Data | Contenuto |
|---|---|---|
| `benchmark_llama_20260918.md/html` | 2026-09-18 | Benchmark singolo modello llama.cpp |
| `benchmark_comparativo_20260918.html` | 2026-09-18 | Confronto multi-modello tok/s |
| `benchmark_llm_pubblico_20260918.html` | 2026-09-18 | Report pubblico completo |
| `benchmark_unsloth_20260926.md/html` | 2026-09-26 | Benchmark Unsloth fine-tune |
| `confronto_epoche_20260926.md` | 2026-09-26 | Confronto epoche training |
| `qwen36_test5_success_20260918.html` | 2026-09-18 | Qwen 3.6 test run |
| `session_completa_6h_20260918.html` | 2026-09-18 | Sessione benchmark 6 ore |
| `test_agentici_comparativi_20260918.html` | 2026-09-18 | Test agentici comparativi |
| `test_agentici_ornith_20260918.html` | 2026-09-18 | Test agentici Ornith |

### LLMs/

| File | Data | Contenuto |
|---|---|---|
| `TestBench-modelli-locali-20260914.md` | 2026-09-14 | Catalogo modelli locali |
| `TestBench-selector-report.md` | 2026-09-11 | Collaudo selettore CUDA tier |

## Metriche

- **tok/s**: token generati al secondo (generation speed)
- **Switch time**: tempo per cambiare modello attivo
- **VRAM**: occupazione memoria GPU
- **TTFT**: time to first token (dove disponibile)

## Licenza

MIT
