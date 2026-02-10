# PROGETTO: Selezione Titoli per Trading Intraday

## Obiettivo
Analizzare il file riepilogo dei 20 titoli e selezionare i migliori per operare intraday il giorno successivo.

---

## FILE DA LEGGERE

```
D:\Progetti\Trading_choose\dati_trading\riepilogo_daily_[DATA].csv
```

**Prendi sempre il file con la DATA più recente** se ce ne sono più di uno.

### Colonne nel file riepilogo:

| Colonna | Descrizione |
|---------|-------------|
| `ticker` | Simbolo del titolo |
| `data` | Data dei dati |
| `close` | Prezzo chiusura oggi |
| `close_ieri` | Prezzo chiusura ieri |
| `var_prezzo_pct` | Variazione % prezzo |
| `sma_20` | Media mobile 20 giorni |
| `sma_50` | Media mobile 50 giorni |
| `vs_sma20_pct` | % distanza da SMA 20 |
| `vs_sma50_pct` | % distanza da SMA 50 |
| `sma20_sopra_sma50` | SI/NO - medie allineate |
| `rsi` | RSI oggi |
| `rsi_ieri` | RSI ieri |
| `rsi_in_salita` | SI/NO |
| `volume` | Volume oggi |
| `volume_sma20` | Media volume 20 giorni |
| `volume_sopra_media` | SI/NO |
| `stoch_k` | Stocastico %K |
| `stoch_d` | Stocastico %D |
| `macd_histogram` | MACD Histogram |

---

## CALCOLO PUNTEGGIO

Per ogni titolo, assegna punti:

| Criterio | Condizione | Punti |
|----------|------------|-------|
| **Sopra SMA 50** | `vs_sma50_pct` > 0 | +3 |
| **Sopra SMA 20** | `vs_sma20_pct` > 0 | +2 |
| **SMA allineate** | `sma20_sopra_sma50` = SI | +2 |
| **RSI sano** | `rsi` tra 40 e 70 | +1 |
| **RSI in salita** | `rsi_in_salita` = SI | +1 |
| **Volume alto** | `volume_sopra_media` = SI | +1 |
| **Vicino supporto** | `vs_sma50_pct` tra 0 e 5 | +1 |
| **Momentum** | `var_prezzo_pct` > 0 | +1 |

**Punteggio massimo: 12 punti**

---

## CLASSIFICAZIONE

| Punteggio | Stato | Significato |
|-----------|-------|-------------|
| 9-12 | ✅ **OPERABILE** | Trend rialzista forte |
| 6-8 | ⚠️ **ATTENDERE** | Trend incerto |
| 0-5 | ⛔ **NON OPERARE** | Trend debole/ribassista |

---

## OUTPUT RICHIESTO

**NON FARE DOMANDE. Leggi il file e fornisci direttamente:**

```
═══════════════════════════════════════════════════════════════════════
📊 SELEZIONE TITOLI - [DATA dal file]
   Per trading intraday di domani
═══════════════════════════════════════════════════════════════════════

✅ OPERABILI (ordinati per punteggio)
┌──────┬────────┬──────────┬──────────┬───────┬────────┐
│ Rank │ Ticker │ Prezzo   │ vs SMA50 │ RSI   │ Punti  │
├──────┼────────┼──────────┼──────────┼───────┼────────┤
│ 1    │ XXXX   │ $XXX.XX  │ +X.X%    │ XX    │ XX/12  │
│ 2    │ XXXX   │ $XXX.XX  │ +X.X%    │ XX    │ XX/12  │
└──────┴────────┴──────────┴──────────┴───────┴────────┘

⚠️ IN ATTESA
┌────────┬──────────┬──────────┬───────┬────────┬─────────────────────┐
│ Ticker │ Prezzo   │ vs SMA50 │ RSI   │ Punti  │ Motivo              │
├────────┼──────────┼──────────┼───────┼────────┼─────────────────────┤
│ XXXX   │ $XXX.XX  │ +X.X%    │ XX    │ X/12   │ RSI in calo         │
└────────┴──────────┴──────────┴───────┴────────┴─────────────────────┘

⛔ NON OPERARE
┌────────┬──────────┬──────────┬───────┬────────┬─────────────────────┐
│ Ticker │ Prezzo   │ vs SMA50 │ RSI   │ Punti  │ Motivo              │
├────────┼──────────┼──────────┼───────┼────────┼─────────────────────┤
│ XXXX   │ $XXX.XX  │ -X.X%    │ XX    │ X/12   │ Sotto SMA 50        │
└────────┴──────────┴──────────┴───────┴────────┴─────────────────────┘

═══════════════════════════════════════════════════════════════════════

🏆 TOP 3 CONSIGLIATI PER DOMANI

1. [TICKER] - Punteggio XX/12
   Prezzo: $XXX.XX | vs SMA50: +X.X% | RSI: XX
   ✓ Sopra SMA 50 ✓ Sopra SMA 20 ✓ RSI in salita ...

2. [TICKER] - Punteggio XX/12
   Prezzo: $XXX.XX | vs SMA50: +X.X% | RSI: XX
   ✓ Sopra SMA 50 ✓ SMA allineate ...

3. [TICKER] - Punteggio XX/12
   Prezzo: $XXX.XX | vs SMA50: +X.X% | RSI: XX
   ✓ Sopra SMA 50 ✓ Volume alto ...

═══════════════════════════════════════════════════════════════════════
💡 RACCOMANDAZIONE: [quale titolo e perché]
═══════════════════════════════════════════════════════════════════════
```

---

## REGOLE IMPORTANTI

1. **Leggi il file `riepilogo_daily_*.csv`** dalla directory - prendi quello con data più recente

2. **Se nessun titolo ha punteggio ≥ 9**:
   - Segnala: "⚠️ Nessun titolo in trend rialzista forte"
   - Suggerisci di NON operare domani
   - Mostra comunque la classifica

3. **Non fare domande** - procedi direttamente con l'analisi

4. **Mostra sempre tutti i 20 titoli** divisi nelle 3 categorie

---

## COMANDI UTENTE

- "Analisi serale" → Esegui selezione completa
- "Quali titoli per domani?" → Esegui selezione completa  
- "Top 3" → Mostra solo i migliori 3
- "Come sta [TICKER]?" → Analizza solo quel titolo

---

## LISTA TITOLI (20)

PLTR, CSCO, AMD, MSFT, META, TSLA, CRWD, PANW, AVGO, NVDA, AMZN, INTC, IONQ, RGTI, QBTS, IREN, GOOGL, AAPL, IBM, NDAQ