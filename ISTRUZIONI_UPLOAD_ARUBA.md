# 📤 ISTRUZIONI UPLOAD ARUBA - MedAxis²

## ✅ IMPORTANTE: Il Build è Pronto!

I file corretti sono già pronti in Replit nella cartella `dist/public/`

File JavaScript cercato: **index-BW5h-SM4.js** ✅  
File CSS cercato: **index-D3msQi--.css** ✅

---

## 📦 STEP 1: Scarica i File da Replit

1. **In Replit**, pannello Files (a sinistra)
2. Vai nella cartella **`dist/public/`**
3. Tasto destro sulla cartella `public/` → **"Download as ZIP"**
4. Salva il file ZIP sul tuo computer
5. **Estrai il contenuto** del ZIP (tasto destro → Estrai qui)

Dovresti avere una cartella `public/` contenente:
```
public/
├── index.html
├── assets/
│   ├── index-BW5h-SM4.js      ← QUESTO è il file giusto!
│   ├── index-D3msQi--.css      ← QUESTO è il file giusto!
│   └── altre immagini...
├── manifest.webmanifest
├── sw.js
├── favicon-96x96.png
├── web-app-manifest-192x192.png
├── web-app-manifest-512x512.png
└── apple-touch-icon-180x180.png
```

---

## 🗑️ STEP 2: Cancella Tutto da Aruba (FileZilla)

**IMPORTANTE**: Devi svuotare completamente la cartella `/medaxis2/` per evitare conflitti con file vecchi.

1. Apri **FileZilla**
2. Connetti ad Aruba con le tue credenziali FTP
3. Nel pannello **DESTRO** (server), naviga fino a: **`/httpdocs/medaxis2/`**
4. **Seleziona TUTTI i file e cartelle** dentro `/medaxis2/`
   - Usa **Ctrl+A** (Windows) o **Cmd+A** (Mac) per selezionare tutto
5. Tasto destro → **"Elimina"**
6. Conferma l'eliminazione
7. **Verifica** che `/medaxis2/` sia completamente **vuota**

---

## 📤 STEP 3: Carica i File Nuovi

**CRUCIALE**: Devi caricare i file **DIRETTAMENTE** in `/medaxis2/`, NON dentro una sottocartella `public/`!

### Metodo Corretto:

1. Sul tuo **computer** (pannello SINISTRO di FileZilla):
   - **Entra DENTRO la cartella `public/`** che hai estratto dal ZIP
   - Dovresti vedere: `index.html`, `assets/`, `manifest.webmanifest`, `sw.js`, `*.png`

2. **Seleziona TUTTI i file** dentro la cartella `public/`:
   - Ctrl+A (Windows) o Cmd+A (Mac)
   - Dovresti aver selezionato: index.html, assets/, manifest.webmanifest, sw.js, tutte le immagini PNG

3. **Trascina** tutti questi file nel pannello **DESTRO** (server) direttamente in **`/httpdocs/medaxis2/`**

4. FileZilla caricherà tutti i file. **Aspetta** che finisca (guarda la barra di progresso in basso).

---

## ✅ STEP 4: Verifica la Struttura su Aruba

Nel pannello **DESTRO** di FileZilla, in `/httpdocs/medaxis2/` dovresti vedere:

```
/httpdocs/medaxis2/
├── index.html                           ← FILE direttamente qui
├── assets/                              ← CARTELLA direttamente qui
│   ├── index-BW5h-SM4.js               ← Verifica che ci sia!
│   ├── index-D3msQi--.css              ← Verifica che ci sia!
│   └── ...
├── manifest.webmanifest                 ← FILE direttamente qui
├── sw.js                                ← FILE direttamente qui
├── favicon-96x96.png                    ← FILE direttamente qui
├── web-app-manifest-192x192.png
├── web-app-manifest-512x512.png
└── apple-touch-icon-180x180.png
```

### ❌ SBAGLIATO (NON deve essere così):
```
/httpdocs/medaxis2/
└── public/              ← NON ci deve essere questa cartella!
    ├── index.html
    └── ...
```

---

## 🧪 STEP 5: Testa il Sito

1. Apri il browser
2. Vai su: **`https://www.neurohrv.it/medaxis2/`**
3. Premi **Ctrl+Shift+R** (Windows) o **Cmd+Shift+R** (Mac) per ricaricare ignorando la cache
4. **Dovresti vedere la landing page di MedAxis²!** 🎉

---

## 🔍 Come Verificare se Funziona

### Test 1: Verifica File JavaScript
Vai su: `https://www.neurohrv.it/medaxis2/assets/index-BW5h-SM4.js`

**Risultato atteso**: Dovresti vedere codice JavaScript (anche se illeggibile perché minificato)  
**Se vedi 404**: I file non sono stati caricati correttamente. Ripeti Step 2 e 3.

### Test 2: Verifica Manifest
Vai su: `https://www.neurohrv.it/medaxis2/manifest.webmanifest`

**Risultato atteso**: Dovresti vedere JSON con "name": "MedAxis²"  
**Se vedi 404**: I file non sono nella posizione giusta.

### Test 3: Console Browser
1. Apri DevTools (F12)
2. Vai nel tab **Console**
3. Ricarica la pagina (Ctrl+R)

**Risultato atteso**: Nessun errore 404  
**Se vedi errori 404**: Verifica che i nomi dei file corrispondano esattamente.

---

## ❓ Problemi Comuni

### ❌ "Failed to load resource: 404" per index-DxB30a3c.js
**Causa**: Hai caricato un build vecchio  
**Soluzione**: Riscarica da Replit e ricarica su Aruba (Ripeti STEP 1-3)

### ❌ Vedo ancora pagina bianca
**Causa 1**: Cache del browser  
**Soluzione**: Premi Ctrl+Shift+Delete → Cancella cache → Ricarica

**Causa 2**: File non nella posizione giusta  
**Soluzione**: Verifica STEP 4 - assicurati che `index.html` sia direttamente in `/medaxis2/`

### ❌ FileZilla non si connette
**Causa**: Credenziali FTP sbagliate o firewall  
**Soluzione**: Verifica username/password nel pannello Aruba o contatta supporto Aruba

---

## 🎯 Checklist Finale

Prima di dire "ho finito":

- [ ] ✅ Ho scaricato `dist/public/` da Replit come ZIP
- [ ] ✅ Ho estratto il contenuto del ZIP
- [ ] ✅ Ho cancellato TUTTO da `/httpdocs/medaxis2/` su Aruba
- [ ] ✅ Sono ENTRATO dentro la cartella `public/` estratta
- [ ] ✅ Ho selezionato TUTTI i file dentro `public/` (index.html, assets/, sw.js, ...)
- [ ] ✅ Ho trascinato i file DIRETTAMENTE in `/httpdocs/medaxis2/`
- [ ] ✅ In `/medaxis2/` vedo `index.html` e `assets/` DIRETTAMENTE (no cartella `public/`)
- [ ] ✅ Dentro `assets/` su Aruba vedo `index-BW5h-SM4.js`
- [ ] ✅ Ho testato `https://www.neurohrv.it/medaxis2/` con Ctrl+Shift+R
- [ ] ✅ Vedo la landing page senza errori 404!

---

## 🎉 Successo!

Se hai completato tutti gli step, MedAxis² dovrebbe essere online e funzionante!

**Prossimo step**: Testare l'installazione PWA su Android (vedi guida separata).

---

## 📞 Se Hai Ancora Problemi

Manda uno screenshot di:
1. FileZilla pannello destro (cartella `/medaxis2/`)
2. Browser console (F12 → tab Console)
3. URL esatto che stai visitando

Buon lavoro! 🚀
