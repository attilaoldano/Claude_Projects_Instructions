# 🔧 Claude Projects - Sistema di Istruzioni Dinamiche

Un pattern per gestire istruzioni dinamiche e aggiornabili nei progetti Claude, senza dover modificare manualmente il progetto ogni volta.

---

## 🎯 Problema Risolto

I progetti Claude hanno istruzioni statiche che richiedono modifica manuale nell'interfaccia. Questo sistema permette di:

- ✅ **Aggiornare le istruzioni** senza toccare il progetto Claude
- ✅ **Versionare** le istruzioni con Git
- ✅ **Condividere** lo stesso set di istruzioni tra più progetti
- ✅ **Separare** configurazione locale da logica di business

---

## 🏗️ Architettura

```
┌─────────────────────────────────────────────────────────────────┐
│                      CLAUDE PROJECT                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────┐    ┌─────────────────────┐            │
│  │  Instructions.txt   │    │ Personalization.txt │            │
│  │  ─────────────────  │    │  ─────────────────  │            │
│  │  URLInstructions =  │    │  DirectoryFile = .. │            │
│  │  https://github...  │    │  ApiKey = ...       │            │
│  └──────────┬──────────┘    │  CustomParam = ...  │            │
│             │               └─────────────────────┘            │
│             │                                                   │
│  ┌──────────▼──────────────────────────────────────────────┐   │
│  │              ISTRUZIONI NEL PROGETTO                    │   │
│  │  ─────────────────────────────────────────────────────  │   │
│  │  "All'inizio di ogni conversazione:                     │   │
│  │   1. Leggi Instructions.txt → estrai URL                │   │
│  │   2. Fetch del file MD da GitHub                        │   │
│  │   3. Applica quelle istruzioni"                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ web_fetch
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                         GITHUB                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  repository/                                                    │
│  └── progetto/                                                  │
│      └── INSTRUCTIONS.md  ◄─── Istruzioni complete e           │
│                                 aggiornabili liberamente        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 Struttura File

### 1. `Instructions.txt` (nel progetto Claude)

Contiene **solo** il puntatore alle istruzioni remote:

```ini
# ============================================
# CONFIGURAZIONE URL ISTRUZIONI
# ============================================

URLInstructions = https://raw.githubusercontent.com/UTENTE/REPO/main/progetto/INSTRUCTIONS.md
```

### 2. `Personalization.txt` (nel progetto Claude)

Contiene i **parametri locali** specifici dell'utente:

```ini
# ============================================
# CONFIGURAZIONE LOCALE
# ============================================

DirectoryFile = D:\Progetti\MioProgetto\dati
ApiEndpoint = https://api.esempio.com
MaxResults = 10
Language = IT
```

### 3. `INSTRUCTIONS.md` (su GitHub)

Contiene la **logica completa** del progetto:

```markdown
# Nome Progetto

## Obiettivo
[Descrizione di cosa deve fare Claude]

## File da Leggere
[Quali file elaborare e dove trovarli]

## Logica di Elaborazione
[Algoritmi, regole, calcoli]

## Output Richiesto
[Formato esatto dell'output]

## Comandi Disponibili
[Lista comandi che l'utente può dare]
```

---

## ⚙️ Istruzioni da Inserire nel Progetto Claude

Copia questo blocco nelle **istruzioni del progetto Claude**:

```markdown
# Istruzioni Dinamiche da GitHub

**All'inizio di OGNI conversazione**, prima di fare qualsiasi cosa:

1. **Leggi il file `Instructions.txt`** allegato al progetto 
   ed estrai il valore di `URLInstructions`

2. **Recupera le istruzioni aggiornate** dall'URL trovato

3. **Leggi e applica TUTTE le istruzioni** contenute nel file scaricato

4. **Procedi con la richiesta dell'utente** seguendo quelle istruzioni

---

## Note operative

- Le istruzioni su GitHub sono la fonte di verità e hanno priorità
- Se il fetch fallisce, informa l'utente e chiedi se vuole riprovare
- Il file `Instructions.txt` deve contenere: `URLInstructions = [URL]`
```

---

## 🚀 Setup Passo-Passo

### 1. Crea il repository GitHub

```bash
# Crea la struttura
mkdir -p claude-instructions/mio-progetto
cd claude-instructions/mio-progetto

# Crea il file istruzioni
touch INSTRUCTIONS.md
```

### 2. Scrivi le istruzioni in `INSTRUCTIONS.md`

```markdown
# Mio Progetto

## Obiettivo
Analizzare i file CSV nella directory configurata...

## Elaborazione
1. Leggi il file più recente
2. Applica la logica X
3. Genera output Y

## Output
[Formato output]
```

### 3. Pubblica su GitHub

```bash
git add .
git commit -m "Istruzioni progetto"
git push origin main
```

### 4. Ottieni l'URL raw

Formato: `https://raw.githubusercontent.com/UTENTE/REPO/BRANCH/PATH/FILE.md`

Esempio: `https://raw.githubusercontent.com/mario/claude-instructions/main/trading/INSTRUCTIONS.md`

### 5. Crea il progetto Claude

1. Vai su **claude.ai** → **Projects** → **New Project**
2. Aggiungi file `Instructions.txt`:
   ```
   URLInstructions = https://raw.githubusercontent.com/...
   ```
3. Aggiungi file `Personalization.txt` con parametri locali
4. Nelle **istruzioni del progetto**, incolla il blocco del punto ⚙️

---

## 📝 Esempi di Utilizzo

### Esempio 1: Progetto di Analisi Dati

**Personalization.txt:**
```ini
DirectoryFile = C:\Users\Mario\Dati\Vendite
OutputFormat = Excel
Language = IT
```

**INSTRUCTIONS.md (GitHub):**
```markdown
# Analisi Vendite

## Leggi da
Directory specificata in `DirectoryFile`

## Elaborazione
1. Carica tutti i CSV
2. Calcola totali per categoria
3. Genera report
```

### Esempio 2: Progetto Multi-Ambiente

**Personalization.txt (DEV):**
```ini
Environment = development
ApiUrl = https://dev.api.example.com
Debug = true
```

**Personalization.txt (PROD):**
```ini
Environment = production
ApiUrl = https://api.example.com
Debug = false
```

Stesso `INSTRUCTIONS.md` per entrambi, comportamento diverso in base ai parametri.

---

## ✅ Vantaggi

| Aspetto | Senza sistema | Con sistema |
|---------|---------------|-------------|
| **Aggiornamenti** | Modifica manuale nel progetto | Push su GitHub |
| **Versioning** | Nessuno | Git history completa |
| **Condivisione** | Copia-incolla | Stesso URL |
| **Rollback** | Impossibile | `git revert` |
| **Collaborazione** | Difficile | PR e review |
| **Parametri locali** | Hardcoded | File separato |

---

## ⚠️ Limitazioni

- **Richiede connessione**: Claude deve poter fare fetch dell'URL
- **Repository pubblico**: L'URL raw deve essere accessibile (o usare token)
- **Rate limits**: GitHub ha limiti sulle richieste raw (ma generosi)
- **Latenza**: Piccolo overhead per il fetch iniziale

---

## 🔒 Per Repository Privati

Se il repository è privato, usa un Personal Access Token:

```ini
URLInstructions = https://raw.githubusercontent.com/UTENTE/REPO/main/INSTRUCTIONS.md
GitHubToken = ghp_xxxxxxxxxxxx
```

E nelle istruzioni del progetto, specifica di usare il token nell'header Authorization.

---

## 📚 Best Practices

1. **Un file INSTRUCTIONS.md per progetto** - Mantieni separazione chiara
2. **Parametri sensibili in Personalization.txt** - Mai su GitHub
3. **Usa branch per versioni** - `main` = stabile, `dev` = test
4. **Documenta i parametri** - Commenta cosa fa ogni parametro
5. **Testa le modifiche** - Verifica su un progetto di test prima

---

## 🤝 Contribuire

Questo pattern è open e adattabile. Sentiti libero di:
- Forkare e personalizzare
- Proporre miglioramenti
- Condividere varianti per casi d'uso specifici

---

## 📄 Licenza

MIT - Usa liberamente per i tuoi progetti.
