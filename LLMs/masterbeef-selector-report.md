# TestBench — collaudo selettore CUDA

Commit pubblicato e verificato su `origin/main`: `6f19e9eb3b2e30ee042b572c35d36bfe5d284757`.

## Risultati reali

| Tier | Attesi tok/s | Misurati (mediana) | Scarto | Switch → ready | Esito |
|---|---:|---:|---:|---:|---|
| Nemotron Cascade 2 | 91.7 | 95.5 | +4.1% | 13.2 s | PASS |
| Qwen 3.5 | 69.5 | 71.4 | +2.8% | 53.4 s | PASS |
| Mellum 2 | 130.5 | 128.7 | -1.4% | 18.6 s | PASS |
| Gemma 4 E4B | 67.8 | 67.4 | -0.6% | 14.1 s | PASS |

## Metodo e limiti

- Richieste reali `/v1/chat/completions`, temperatura 0; tre campioni da 256 token per tier, metrica generation tok/s restituita da llama.cpp. Per stabilizzare la lunghezza: ignore_eos=true nel solo benchmark.
- Prova separata con max_tokens=2048, EOS normale, verifica di una risposta finale non vuota: tutti i modelli hanno risposto Parigi. Nessuna conclusione sul tool-calling di [AGENT] è dedotta da questa prova.
- Tempi di switch misurati dal POST /switch fino allo stato ready verificato: cache filesystem potenzialmente calda. Non sono garanzie di cold boot.
- Tra tutti gli switch: VRAM tornata a GPU0 559 MiB / GPU1 126 MiB; nessuna crescita residua. Un solo llama-server.exe, PID corrispondente al manager e al modello attivo.
- /models contiene sempre quattro tier available=true; /v1/models e le risposte usano alias senza percorsi GGUF.
- Restore finale: Nemotron Cascade 2 READY; prova via VPN da [SERVER] restituisce OK.
- Vecchio launcher VR: incompatibile con il nuovo processo CUDA llama-server.exe; rifiuta in sicurezza. Non modificato in questo task.

## Verifiche software

- Suite Linux: 1020 PASS, 1 skipped, 0 errori/fallimenti.
- Suite manager Windows: 18 PASS (server e HTTP). La suite completa harness non è stata eseguita sul PC aziendale.
- Chromium + API live del manager: quattro opzioni, nomi leggibili, nessuna opzione disabilitata, Cascade selezionato; screenshot selector-browser.png.
- Secret scan e git diff --check passati.

## Evidenze

- TestBench-selector-acceptance.json: tutti i campioni e snapshot GPU.
- selector-final-state.json: hash remoto, task e processo finale.
- selector-final-mesh.json: prova HTTP e inferenza via VPN.
- selector-suite.xml: suite Linux.
- selector-browser.json e selector-browser.png: verifica selettore.
- Backup manager precedente: C:\Users\[USER]\bench\manager-backup-<timestamp>\.
- Sorgenti riproducibili: scripts/benchmark_model_selector.py, scripts/deploy-TestBench-manager.ps1.
