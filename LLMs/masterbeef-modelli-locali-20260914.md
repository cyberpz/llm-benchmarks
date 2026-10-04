# TestBench — Modelli locali LM Studio + stato disco

**Data:** 14/09/2026 21:01 CEST
**Host:** TestBench (Windows, SSH `[USER]@[LAN_IP]`)
**Metodo:** `lms ps`, `lms ls`, `Get-Volume`, scan ricorsivo (629.869 file), spostamenti verificati voce per voce
**Autore:** Domus

---

## 1. Modelli in locale (LM Studio) — 9 modelli, 88,01 GB

| GB | Modello | Params | Arch | Ruolo | Tagliabile |
|---:|---|---|---|---|---|
| 22,02 | `qwen3.5-35b-a3b` | 35B-A3B | qwen35moe | **produzione** ([AGENT] / [CLIENT]) | no |
| 19,79 | `nvidia-nemotron-3.5-lightning-30b-a3b-nvfp4-nomtp` | 128x2.4B | nemotron_h_moe | sperimentale | sì |
| 18,27 | `nvidia_nemotron-cascade-2-30b-a3b` | 30B-A3B | nemotron_h_moe | sperimentale | sì |
| 8,07 | `mellum2-12b-a2.5b-thinking` | 12B-A2.5B | mellum | **default router GPU1** | no |
| 6,73 | `flux2-klein-9b-uncensored-text-encoder` | 9B | qwen3 | text-encoder per Flux (non LLM) | sì |
| 6,33 | `gemma-4-e4b-uncensored-hauhaucs-aggressive` | 7.5B | gemma4 | provabile | sì |
| 6,21 | `huihui-nvidia-nemotron-nano-9b-v2-abliterated-i1` | 9B | nemotron_h | provabile | sì |
| 0,51 | `text-embedding-nomic-embed-text-v2-moe` | 8x277M | nomic-bert-moe | embeddings | no |
| 0,08 | `text-embedding-nomic-embed-text-v1.5` | Nomic BERT | — | embeddings | no |

**Totale: 88,01 GB** (quota su disco riportata da LM Studio).

- `lms ps` → **vuoto**: nessun modello caricato *da* LM Studio. L'inferenza di produzione gira su `llama-server` esterno ([AGENT]ModelManager), endpoint `:1234` → HTTP 200.
- Candidati al taglio (solo se non servono): 2 Nemotron 30B (38,06 GB) + text-encoder Flux (6,73) + gemma-4-e4b (6,33) + nano-9b abliterated (6,21) = **57,33 GB**.
- Nessuna cancellazione necessaria: la cartella `.lmstudio\models` è spostabile su `D:` → libera tutti gli 88 GB.

---

## 2. Stato disco

| | Prima | Dopo |
|---|---:|---:|
| `C:` liberi | 0,10 GB / 237,8 GB | **24,41 GB** |
| `D:` liberi (SD-Forge) | 229,73 GB / 238,5 GB | 205,51 GB |

### Chi occupava C: (prima della pulizia)

| GB | Percorso |
|---:|---|
| 131,41 | `C:\Users\[USER]` — di cui `.lmstudio\models` 84,14 + AppData\Local 22,48 + Downloads 9,33 + [PROJECT]\venv 5,73 |
| 37,30 | `C:\Program Files (x86)` — di cui Steam 26,38 |
| 36,45 | `C:\Windows` — WinSxS 10,02, System32 12,31 |
| 17,47 | `C:\Program Files` |
| 13,61 | `C:\pagefile.sys` |
| 3,02 | `C:\ProgramData` |
| 2,53 | `C:\NVIDIA` |

Nessuna shadow copy, recycle bin vuoto, `hiberfil.sys` assente.

---

## 3. Pulizia eseguita — 24,16 GB in quarantena su D:

**Nulla è stato cancellato.** Tutto spostato in `D:\_quarantine_20260914` con `RESTORE_README.txt` che mappa ogni voce al path originale.

| GB | Voce in quarantena | Path originale |
|---:|---|---|
| 9,29 | `01_chrome.crdownload` | `C:\Users\[USER]\Downloads\Non confermato 713856.crdownload` |
| 2,56 | `02_hf_incomplete.blob` | `%SYSTEMPROFILE%\.cache\huggingface\…\faster-whisper-large-v3\blobs\*.incomplete` |
| 2,53 | `03_NVIDIA_DisplayDriver` | `C:\NVIDIA\DisplayDriver` |
| 2,26 | `04_lmstudio.part` | `.lmstudio\…\Qwen3.5-9B-GGUF\downloading_….part` |
| 1,97 | `05_CrashDumps` | `AppData\Local\CrashDumps` |
| 1,18 | `06_MEMORY.DMP` | `C:\Windows\MEMORY.DMP` |
| 0,87 | `07_LiveKernelReports` | `C:\Windows\LiveKernelReports` |
| 0,86 | `08_VRChat_cache` | `AppData\LocalLow\VRChat\VRChat\Cache-WindowsPlayer` |
| 0,67 | `09_npm_cacache` | `AppData\Local\npm-cache\_cacache` |
| 0,52 | `11_lmstudio_updater` | `AppData\Local\lm-studio-updater` |
| 0,51 | `10_brave_chrome.7z` | `Program Files\BraveSoftware\…\Installer\chrome.7z` |
| 0,47 | `12_uv_cache` | `AppData\Local\uv\cache` |
| 0,43 | `20_Windows.edb` + `21_edb*.jtx/jcp/jrs/jfm` | `ProgramData\Microsoft\Search\Data\Applications\Windows\` |
| 0,03 | `13_torch_cache` | `.cache\torch` |

Più **0,64 GB di Temp** cancellati a caldo (`Local\Temp` + `Windows\Temp`; i file lockati lasciati stare).
Quarantena totale: **24,16 GB / 18.478 file**. Delta atteso ~24,8 GB, rilevati 24,41: il resto riassorbito dal sistema durante le operazioni (rebuild indice, log).

### Ripristino

`D:\_quarantine_20260914\RESTORE_README.txt` contiene la mappa voce → path originale. Ripristino per voce con `robocopy` (directory) o `[IO.File]::Move` (file).

---

## 4. Punti d'attenzione registrati durante l'operazione

| Problema | Causa | Stato |
|---|---|---|
| Primo giro di spostamento: 13/13 `MISS` | funzione PowerShell chiamata `MV` = **alias built-in di `Move-Item`** (gli alias battono le funzioni) → `-Destination` relativo risolto sulla CWD `C:\Users\[USER]`, item rinominati `01_…13_` | recuperati tutti con ricognizione su 7 directory, poi spostati correttamente. **Zero perdite** |
| `WSearch` non ripartito dopo lo stop | stop forzato con `Windows.edb` aperto → evento SCM **7031** (arresto imprevisto) + recovery 30 s; `Start-Service` troppo precoce = no-op | retry verificato: **Running** / `AUTO_START (DELAYED)`, `SearchIndexer` + `SearchProtocolHost` + `SearchFilterHost` attivi, indice in rebuild |
| `[PROJECT]`, `DomusSTT`, `DomusEarDaemon` = Stopped | `Manual` dal 25/08/2026, exit code **1077** (mai avviato), log fermi al 07/09 | **estranei alla pulizia**, erano già fermi |

Verifica di integrità post-operazione: `localhost:1234/v1/models` → HTTP 200; `llama-server` + LM Studio + `[AGENT]-model-manager.py` attivi; `.lmstudio\models`, venv `[PROJECT]`, repo e modelli **non toccati**; blob `faster-whisper` completo (2,88 GB) intatto.

---

## 5. Prossimi passi (non eseguiti)

| GB | Intervento | Come | Note |
|---:|---|---|---|
| 88,01 | `.lmstudio\models` → `D:` | LM Studio → My Models → change directory | nessuna cancellazione; destinazione da confermare (`D:\lmstudio-models`) |
| 26,38 | Steam (giochi VR) → `D:` | Steam → Impostazioni → Archivio → aggiungi libreria `D:\SteamLibrary` | 15 titoli; richiede passaggio manuale/UI |
| 13,61 | `pagefile.sys` → `D:` | Memoria virtuale su D: o 8 GB fissi | richiede reboot |
| 3–7 | `WinSxS` / `Windows\Installer` | `DISM /Online /Cleanup-Image /StartComponentCleanup /ResetBase` | pulizia componenti, irreversibile ma sicura |

Con LM Studio + Steam + pagefile: **~145 GB liberi su C:**, `D:` con ~60 GB residui.

---

*Report generato da Domus — dati verificati con output reali su TestBench, nessun valore stimato senza etichetta.*