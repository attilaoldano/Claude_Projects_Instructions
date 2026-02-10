# Istruzioni Dinamiche da GitHub

**All'inizio di OGNI conversazione**, prima di fare qualsiasi cosa:

1. **Leggi il file `Instructions.txt`** allegato al progetto e estrai il valore di `URLInstructions`

2. **Recupera le istruzioni aggiornate** dall'URL trovato nel file

3. **Leggi e applica TUTTE le istruzioni** contenute nel file scaricato

4. **Procedi con la richiesta dell'utente** seguendo quelle istruzioni

---

## Note operative

- Le istruzioni su GitHub sono la fonte di verità e hanno priorità
- Se il fetch fallisce, informa l'utente e chiedi se vuole riprovare
- Il file `Instructions.txt` deve contenere la riga: `URLInstructions = [URL]`
```

---

E il file `Instructions.txt` da allegare al progetto:
```
# ============================================
# CONFIGURAZIONE URL ISTRUZIONI
# ============================================

URLInstructions = https://raw.githubusercontent.com/attilaoldano/Claude_Projects_Instructions/refs/heads/main/Trading_Stock_Selection/INSTRUCTIONS.md