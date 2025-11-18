# 🚀 UPLOAD MedAxis² su Aruba - Istruzioni FINALI

## ✅ STEP 1: Scarica Build da Replit

1. Nel pannello **Files** di Replit (lato sinistro)
2. Naviga su **`dist/public/`**
3. Tasto destro sulla cartella **`public/`**
4. Click **"Download as ZIP"**
5. Salva sul computer
6. **Estrai** tutto il contenuto

---

## 📂 STEP 2: Verifica File Scaricati

Nella cartella `public/` estratta DEVI vedere:

```
public/
├── .htaccess                    ← CRUCIALE! (file nascosto)
├── index.html                   ← Modificato con /medaxis2/
├── manifest.webmanifest
├── sw.js
├── assets/
│   ├── index-61Gp38Dc.js
│   └── index-D3msQi--.css
└── *.png (icone PWA)
```

### ⚠️ Mostra File Nascosti

**Windows**: Esplora File → Visualizza → "Elementi nascosti" ✓  
**Mac**: Finder → `Cmd + Shift + .` (punto)

---

## 🗑️ STEP 3: Cancella Vecchi File da Aruba

1. Apri **FileZilla**
2. Connetti ad Aruba
3. Vai su **`/httpdocs/medaxis2/`**
4. Seleziona **TUTTO** (Ctrl+A)
5. Elimina
6. Verifica cartella **vuota**

---

## 📤 STEP 4: Carica Nuovi File

1. **PC**: Apri cartella `public/` estratta
2. **FileZilla**:
   - Pannello sinistro: `public/` locale
   - Pannello destro: `/httpdocs/medaxis2/` Aruba
3. **Seleziona TUTTO** in `public/` (Ctrl+A)
4. **Trascina** da sinistra a destra
5. **Attendi** fine caricamento

---

## ✅ STEP 5: Verifica su Aruba

In `/httpdocs/medaxis2/` deve esserci:

```
.htaccess           ← IMPORTANTE!
index.html
assets/
manifest.webmanifest
sw.js
*.png
```

**FileZilla non mostra .htaccess?**  
→ Menu **"Server"** → **"Forza visualizzazione file nascosti"**

---

## 🧪 STEP 6: Testa il Sito

1. Apri browser
2. Vai su: **`https://www.neurohrv.it/medaxis2/`**
3. Premi **Ctrl+Shift+R** (ricarica completa)

### ✅ Cosa DEVI Vedere:

- ✅ **Landing page MedAxis²** con logo
- ✅ Testo e layout corretti
- ✅ Nessun errore CSS/JS (pagina non bianca)

### ⚠️ Errori NORMALI (ignora):

```
❌ GET /api/auth/user - 404
❌ GET /api/... - Failed
```

**Motivo**: Backend non c'è ancora (normale, prossimo step Railway)

---

## ❌ Problemi?

### 1. Pagina Bianca

**Causa**: File .htaccess o path sbagliati

**Soluzione**:
- Verifica `.htaccess` presente su Aruba
- Ricarica con Ctrl+Shift+R
- Verifica permessi file: 644

### 2. Errore 404

**Causa**: `.htaccess` non funziona

**Soluzione**:
- Forza visualizzazione nascosti FileZilla
- Verifica `.htaccess` caricato
- Contatta supporto Aruba (mod_rewrite attivo?)

### 3. CSS/JS non caricano

**Causa**: Path sbagliati

**Soluzione**:
- Scarica di nuovo da Replit
- Ricarica su Aruba
- Ctrl+Shift+R nel browser

---

## 🎯 Prossimi Passi

1. ✅ **Fatto**: Frontend su Aruba
2. 🔜 **Next**: Backend su Railway
3. 🔜 **Finale**: Collega frontend ↔ backend

---

## 💡 Note

- Landing page funzionerà ✅
- Login/Dashboard NON funzioneranno fino a Railway ❌
- Questo è **NORMALE**
- Railway risolverà tutto ✨

---

**Dimmi quando vedi la landing page su Aruba!** 🚀
