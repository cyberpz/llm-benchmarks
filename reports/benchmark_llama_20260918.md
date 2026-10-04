# Benchmark Modelli LLM su TestBench
**Data:** 2026-09-18  
**Hardware:** 2x RTX 3060 12GB, i7-4770K, 32GB RAM  
**Binario:** llama.exe serve (llama.cpp via WindowsApps)  
**Backend:** Vulkan (non CUDA)

---

## Risultati Test

### Qwen3.8-27B-GSQ-RCO-IQ2_XS (8.4GB)
**Configurazione:** context 8192, device Vulkan0, ngl 999

| Metrica | Valore | Note |
|---------|--------|------|
| **Prompt Processing** | 51.14 tok/s | Eccellente |
| **Generazione** | 17.27 tok/s | Buona |
| **TTFT** | 1.06s | Time to first token |
| **VRAM utilizzata** | ~8.8GB | Su singola GPU |
| **RAM totale** | ~2GB | Processo Python |

**Test specifici:**
- Ragionamento: 15/100 (thinking mode attivo, risposte in reasoning_content)
- Codice: 45/100 (16.93 tok/s, TTFT 64s)
- Creatività: 25/100 (16.72 tok/s, TTFT 63s)
- Istruzioni: 15/100 (16.32 tok/s, TTFT 38s)
- Matematica: 15/100 (timeout)
- Conoscenza: 100/100 (16.33 tok/s, TTFT 26s, 643 token)

**Osservazioni:**
- ⚠️ Thinking mode attivo: risposte in `reasoning_content` invece di `content`
- ⚠️ TTFT elevato (26-64s) per prompt complessi
- ✅ Prompt processing veloce (51 tok/s)
- ✅ Generazione stabile (17 tok/s)
- ⚠️ Punteggi bassi nei test strutturati (15-45/100)

---

### Qwen3.6-35B-A3B-Uncensored (21GB)
**Stato:** ❌ Non caricabile in VRAM

**Problemi:**
- Modello troppo grande per 2x RTX 3060 (24GB totali)
- Con context 8k: crash immediato
- Con context 4k: crash immediato
- Anche con 2 GPU (Vulkan0,Vulkan1): non si avvia

**Note:**
- Richiederebbe quantizzazione più aggressiva (IQ2_XS o simile)
- Oppure CPU offload parziale (lento)

---

### Ornith-1.5-35B-A3B (22GB)
**Stato:** ❌ Non testato (stesso problema di Qwen3.6)

---

## Analisi Prestazioni

### Confronto Context Size (Qwen3.8)

| Context | Prompt Speed | Gen Speed | VRAM | Note |
|---------|--------------|-----------|------|------|
| 32768 | 0.29 tok/s | 17 tok/s | ~10GB | ❌ Troppo lento |
| 8192 | 51.14 tok/s | 17.27 tok/s | ~8.8GB | ✅ Ottimale |

**Miglioramento 8k vs 32k:**
- Prompt processing: **176x più veloce** (0.29 → 51.14 tok/s)
- Generazione: invariata (~17 tok/s)
- VRAM: -1.2GB

### IQ2_XS vs Q4_K_M (stesso modello)

**Aspettativa:** IQ2_XS dovrebbe essere 2x più veloce (metà peso)  
**Realtà:** Stessa velocità di generazione (17 tok/s)

**Possibili cause:**
1. Thinking mode overhead (ragionamento interno)
2. Vulkan backend meno ottimizzato di CUDA
3. Bottleneck CPU (i7-4770K vecchio)
4. Quantizzazione IQ2_XS meno efficiente del previsto

---

## Problemi Identificati

### 1. Thinking Mode
- Modello genera `reasoning_content` invece di `content`
- TTFT elevato (26-64s)
- Punteggi bassi nei test strutturati
- **Soluzione:** Disabilitare con `--no-display-prompt` o simile

### 2. Vulkan Backend
- Meno ottimizzato di CUDA
- Potenziale perdita di prestazioni 10-20%
- **Soluzione:** Installare llama.cpp con supporto CUDA nativo

### 3. Modelli 35B non caricabili
- 21-22GB troppo grandi per 24GB VRAM totale
- Overhead sistema + context = impossibile
- **Soluzione:** Quantizzazione IQ2_XS o CPU offload

### 4. Encoding Windows
- Benchmark crash su caratteri Unicode
- **Soluzione:** Usare `chcp 65001` o UTF-8 encoding

---

## Raccomandazioni

### Immediate
1. ✅ Usare context 8k (ottimale per velocità/qualità)
2. ⚠️ Disabilitare thinking mode per risposte dirette
3. ⚠️ Testare modelli più piccoli (7-14B) per velocità massima

### Medio termine
1. Installare llama.cpp con CUDA nativo (non Vulkan)
2. Quantizzare Qwen3.6 e Ornith a IQ2_XS (8-10GB)
3. Aggiornare CPU (i7-4770K è bottleneck)

### Lungo termine
1. Considerare GPU più grande (RTX 4090 24GB o A6000 48GB)
2. Oppure multi-GPU con NVLink (costoso)
3. Valutare cloud inference per modelli >20GB

---

## Comandi Utili

### Avvio modello (context 8k)
```batch
C:\Users\[USER]\AppData\Local\Microsoft\WindowsApps\llama.exe serve ^
  -m D:\Models\Qwen3.8-27B-GSQ-RCO-IQ2_XS.gguf ^
  -c 8192 ^
  --host 127.0.0.1 ^
  --port 1234 ^
  -ngl 999 ^
  --device Vulkan0 ^
  --threads 8
```

### Test rapido
```batch
curl -s http://localhost:1234/v1/chat/completions ^
  -H "Content-Type: application/json" ^
  -d "{\"model\":\"test\",\"messages\":[{\"role\":\"user\",\"content\":\"Hello\"}],\"max_tokens\":50}"
```

### Benchmark completo
```batch
cd C:\Users\[USER]
python benchmark.py --models direct
```

---

## Conclusioni

**Qwen3.8-27B-GSQ-RCO-IQ2_XS:**
- ✅ Utilizzabile con context 8k
- ✅ Prompt processing veloce (51 tok/s)
- ⚠️ Generazione buona ma non eccezionale (17 tok/s)
- ⚠️ Thinking mode rallenta risposte complesse
- ⚠️ Punteggi qualità bassi (15-45/100)

**Modelli 35B:**
- ❌ Non caricabili in VRAM attuale
- ❌ Richiedono quantizzazione estrema o hardware superiore

**Prossimi step:**
1. Testare disabilitazione thinking mode
2. Quantizzare modelli 35B a IQ2_XS
3. Valutare modelli 7-14B per velocità massima

---

**Report generato:** 2026-09-18  
**Strumenti:** llama.cpp (Vulkan), Python 3.10, Windows 10
