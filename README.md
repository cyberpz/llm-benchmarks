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
INDEX.md          # Unified ranking by gen tok/s
models/           # One file per model with all epochs
reports/          # Original benchmark reports (sanitized)
LLMs/             # Local model catalog
docs/             # Conventions (see CONTRIBUTING.md)
```

## How to add benchmark data

See [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md) — naming conventions, sanitization rules, epoch system.

## Report disponibili

### reports/

| File | Data | Contenuto |
|---|---|---|
| `benchmark_llama_20260918.md` | 2026-09-18 | Benchmark singolo modello llama.cpp |
| `benchmark_unsloth_20260926.md` | 2026-09-26 | Benchmark Unsloth engine |
| `confronto_epoche_20260926.md` | 2026-09-26 | Confronto epoche Vulkan/CUDA/Unsloth |

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
