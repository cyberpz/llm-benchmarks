# Contributing — How to add benchmark data

This document defines the conventions for adding/updating LLM benchmark data in this repo.

## Structure

```
INDEX.md               # Unified ranking by gen tok/s, link to every model
models/<ModelName>.md  # One file per model, all epochs
reports/               # Original benchmark reports (sanitized)
LLMs/                  # Local model catalog and GPU selector reports
```

## Adding a new benchmark

1. **Extract the data** from the source report (MD/HTML/log)
2. **Create/update** `models/<ModelName>.md` with the standard table:
   ```
   | Epoch | Date | Backend | Gen tok/s | Prompt tok/s | TTFT s | Size GB | Config |
   ```
3. **Update INDEX.md** — insert the model in the ranking ordered by gen tok/s (E3 default config)
4. **Sanitize** before committing: no user paths, IPs, real machine names, client names
5. **Commit + push** to main

## Model file naming

- Exact GGUF/model name, slash → hyphen: `Qwen3.5-35B-A3B-Q4_K_M.md`
- Preserve the model's original case

## Mandatory sanitization

Before every push, verify absence of:
- Personal Windows/Linux usernames
- Absolute paths containing usernames (`C:\Users\...`, `/home/...`)
- LAN/WireGuard/VPN IPs
- Real machine names
- Client or internal project names
- Telegram handles / emails

Use generic placeholders: `[USER]`, `[HOME]`, `[LAN_IP]`, `[SERVER]`, `[CLIENT]`, `[AGENT]`

## Epoch naming

- **E1**: initial Vulkan/LM Studio tests (September 2026)
- **E2**: CUDA selector tier validation
- **E3**: Unsloth engine, np4 fit-auto spec-decode flash-attn
- **E3-tuned**: np1 full-VRAM no-fit (production)
- New epochs: increment (E4, E5...) with config description in notes

## Metrics

- **Gen tok/s**: generation speed (always present)
- **Prompt tok/s**: prompt processing (optional)
- **TTFT**: time to first token (optional)
- **Size GB**: model weight on disk (optional)
- If a value is missing, use `-` in the table — never invent data
