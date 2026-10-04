<div align="center">

# ⚡ LLM Inference Benchmarks

**Real-world speed measurements on consumer hardware — no estimates, only measured tok/s**

![Models tested](https://img.shields.io/badge/models_tested-14-blue)
![Benchmark runs](https://img.shields.io/badge/benchmark_runs-34-green)
![Epochs](https://img.shields.io/badge/epochs-4-orange)
![Backends](https://img.shields.io/badge/backends-CUDA_%7C_Vulkan-purple)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

</div>

---

## 📊 At a Glance

| | |
|---|---|
| **Models tested** | 14 (13 benchmarked, 1 load failure) |
| **Total benchmark runs** | 34 |
| **Test dates** | 2026-09-11 · 2026-09-18 · 2026-09-26 |
| **Backends** | llama.cpp CUDA (26 runs) · CUDA selector (5) · Vulkan/LM Studio (3) |
| **Speed range** | 13.8 – 185.8 tok/s generation |

## 🖥️ Hardware

| Component | Spec |
|---|---|
| GPU | 2× NVIDIA RTX 3060 12GB |
| CPU | AMD Ryzen 7 5800X |
| RAM | 64GB DDR4-3600 |
| OS | Windows 11 Pro |
| Stack | llama.cpp (CUDA/Vulkan), LM Studio, Unsloth engine |

## 🏆 Generation Speed Ranking (E3 default)

| # | Model | Gen tok/s | Prompt tok/s | Size GB |
|---|---|---|---|---|
| 🥇 | [Qwen2.5-1.5B-Instruct-Q4_K_M](models/Qwen2.5-1.5B-Instruct-Q4_K_M.md) | **185.8** | 3463.5 | 1.04 |
| 🥈 | [Spark-X2.5-4B-Q8_0](models/Spark-X2.5-4B-Q8_0.md) | **171.3** | 1530.9 | 4.07 |
| 🥉 | [Mellum2-12B-A2.5B-Thinking-Q4_K_M](models/Mellum2-12B-A2.5B-Thinking-Q4_K_M.md) | **134.9** | 995.6 | 7.52 |
| 4 | [gemma-4-E4B-it-qat-UD-Q4_K_XL](models/gemma-4-E4B-it-qat-UD-Q4_K_XL.md) | 117.7 | 1349.9 | 3.93 |
| 5 | [MiniCPM5-2B-heretic-abli-Q8_0](models/MiniCPM5-2B-heretic-abli-Q8_0.md) | 105.5 | 2327.1 | 2.50 |
| 6 | [Nemotron-Cascade-2-30B-A3B-Q4_0](models/Nemotron-Cascade-2-30B-A3B-Q4_0.md) | 90.5 | 459.0 | 17.02 |
| 7 | [Qwen3.6-35B-A3B-Uncensored-Q4_K_M](models/Qwen3.6-35B-A3B-Uncensored-Q4_K_M.md) | 79.6 | 499.3 | 19.71 |
| 8 | [Nemotron-3.5-Lightning-30B-A3B-NVFP4](models/Nemotron-3.5-Lightning-30B-A3B-NVFP4.md) | 73.1 | 314.4 | 18.43 |
| 9 | [Gemma-4-E4B-Uncensored-Q4_K_M](models/Gemma-4-E4B-Uncensored-Q4_K_M.md) | 72.0 | 1268.2 | 4.97 |
| 10 | [Huihui-Nemotron-Nano-9B-v2-abli-Q4_K_S](models/Huihui-Nemotron-Nano-9B-v2-abli-Q4_K_S.md) | 47.1 | 734.4 | 5.79 |
| 11 | [Qwen3.5-35B-A3B-Q4_K_M](models/Qwen3.5-35B-A3B-Q4_K_M.md) | 34.8 | 155.9 | 20.50 |
| 12 | [Ornith-1.5-35B-Q4_K_M](models/Ornith-1.5-35B-Q4_K_M.md) | 26.5 | 145.9 | 20.22 |
| 13 | [Qwen3.8-27B-GSQ-RCO-IQ2_XS](models/Qwen3.8-27B-GSQ-RCO-IQ2_XS.md) | 22.2 | 233.7 | 7.84 |
| — | [Ternary-Bonsai-2-27B-PQ2_0](models/Ternary-Bonsai-2-27B-PQ2_0.md) | ❌ load failure | — | 6.71 |

Full index with per-model links: [INDEX.md](INDEX.md)

## 🧪 Test Epochs

<details>
<summary><b>E1 — Vulkan / LM Studio (2026-09-18)</b> · 3 runs</summary>

First tests on Vulkan backend via LM Studio, `ngl 999`. Baseline for large MoE models; some crashes at ctx > 4k.

</details>

<details>
<summary><b>E2 — CUDA selector (2026-09-11)</b> · 5 runs</summary>

CUDA backend validation with GPU selector tier, `np1`, fit off. Context 8192.

</details>

<details>
<summary><b>E3 — Unsloth engine, default (2026-09-26)</b> · 13 runs</summary>

Unsloth engine, `np4`, fit auto, spec-decode, flash-attn. The main comprehensive run: all 14 models, prompt + generation speed, ctx 8192.

</details>

<details>
<summary><b>E3-tuned — production config (2026-09-26)</b> · 13 runs</summary>

`np1`, full-VRAM, no fit — production serving configuration. Up to 2.4× faster than E3 default on MoE models (e.g. Ornith-1.5-35B: 26.5 → 63.3 tok/s).

</details>

## 📁 Repository Structure

```
INDEX.md          Unified ranking by gen tok/s
models/           One file per model — all epochs, configs, notes
reports/          Original benchmark reports (sanitized)
LLMs/             Local model catalog + GPU selector reports
docs/             Conventions for contributing
```

## 📈 Metrics

| Metric | Meaning |
|---|---|
| **Gen tok/s** | Generation speed (tokens/second) — always measured |
| **Prompt tok/s** | Prompt processing speed |
| **TTFT** | Time to first token (seconds) |
| **Size GB** | Model weight on disk |

> Missing values are marked `-`. No estimates, no invented data.

## 🔧 Contributing

See [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md) — file naming, sanitization rules, epoch system.

---

<div align="center">

*All numbers measured on real hardware. Your mileage may vary with different drivers, VRAM state, and thermals.*

**MIT License**

</div>
