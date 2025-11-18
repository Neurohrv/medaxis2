# 🚀 Istruzioni Upload MedAxis² su Aruba Linux Base

## ✅ PASSO 1: Scarica File da Replit

1. Nel pannello **Files** di Replit (a sinistra)
2. Naviga su **`dist/public/`**
3. Tasto **destro** sulla cartella `public/`
4. Click su **"Download as ZIP"**
5. Salva il file sul tuo computer
6. **Estrai** il contenuto dello ZIP

---

## 📂 PASSO 2: Verifica File Estratti

Nella cartella `public/` estratta DEVI vedere:

```
public/
├── .htaccess                    ← IMPORTANTE! (file nascosto)
├── index.html
├── manifest.webmanifest
├── sw.js
├── assets/
│   ├── index-61Gp38Dc.js       (nome può variare)
│   └── index-D3msQi--.css      (nome può variare)
├── favicon-96x96.png
├── web-app-manifest-192x192.png
├── web-app-manifest-512x512.png
├── apple-touch-icon-180x180.png
└── altri file .png/.svg/.ico
```

### ⚠️ IMPORTANTE: Mostra File Nascosti

Il file **`.htaccess`** inizia con un punto → Potrebbe essere nascosto!

**Su Windows**:
1. Apri Esplora File
2. Tab "Visualizza" → Spunta "Elementi nascosti"

**Su Mac**:
1. Nel Finder premi: `Cmd + Shift + .` (punto)

---

## 🗑️ PASSO 3: Cancella Tutto da Aruba (FileZilla)

1. Apri **FileZilla**
2. Connetti ad Aruba (FTP)
3. Naviga su **`/httpdocs/medaxis2/`**
4. **Seleziona TUTTI i file** (Ctrl+A o Cmd+A)
5. Tasto destro → **Elimina**
6. Conferma eliminazione
7. Verifica che la cartella sia **completamente vuota**

---

## 📤 PASSO 4: Carica File Nuovi

1. **Sul tuo PC**: 
   - Apri la cartella `public/` estratta
   - **Entra DENTRO** la cartella (devi vedere index.html, assets/, ecc.)

2. **In FileZilla**:
   - Pannello **sinistro**: La cartella `public/` sul tuo PC
   - Pannello **destro**: `/httpdocs/medaxis2/` su Aruba

3. **Seleziona TUTTI i file** dentro `public/`:
   - Ctrl+A (o Cmd+A su Mac)
   - Includi: .htaccess, index.html, assets/, manifest.webmanifest, sw.js, *.png

4. **Trascina** dal pannello sinistro al pannello destro

5. **Attendi** il completamento del caricamento

---

## ✅ PASSO 5: Verifica su FileZilla

In **`/httpdocs/medaxis2/`** su Aruba devi vedere:

```
/httpdocs/medaxis2/
├── .htaccess                    ← CRUCIALE!
├── index.html
├── assets/
│   ├── index-61Gp38Dc.js
│   └── index-D3msQi--.css
├── manifest.webmanifest
├── sw.js
└── *.png (varie icone)
```

### 🔍 Come Vedere .htaccess in FileZilla

Se NON vedi il file `.htaccess`:

1. FileZilla → Menu in alto **"Server"**
2. Click su **"Forza visualizzazione file nascosti"**
3. Ora dovresti vederlo!

---

## 🧪 PASSO 6: Testa il Sito

1. Apri browser (Chrome/Firefox/Safari)
2. Vai su: `https://www.neurohrv.it/medaxis2/`
3. Premi **Ctrl+Shift+R** (Windows) o **Cmd+Shift+R** (Mac) per ricaricare
4. **Risultato atteso**:
   - ✅ Vedi la landing page MedAxis²
   - ⚠️ Login/Dashboard NON funzioneranno (normale, serve backend)

---

## ❓ Problemi Comuni

### Problema: Vedo ancora 404

**Soluzione 1**: Verifica .htaccess
- Su FileZilla, controlla che `.htaccess` sia presente in `/httpdocs/medaxis2/`
- Forza visualizzazione file nascosti (Menu Server)

**Soluzione 2**: Ricarica con cache pulita
- Ctrl+Shift+R (Windows) o Cmd+Shift+R (Mac)
- O: Ctrl+F5 (Windows)

**Soluzione 3**: Verifica permessi file
- Tasto destro su `.htaccess` → Permessi file
- Deve essere: **644** (rw-r--r--)

### Problema: Errori API in console

✅ **NORMALE!** Le API non funzionano senza backend.

Vedrai errori tipo:
```
❌ GET /api/auth/user - 404
❌ GET /api/... - Failed
```

**Soluzione**: Ignorali per ora. Il prossimo passo è configurare Railway per il backend.

---

## 🎯 Prossimi Passi

Dopo che il frontend è visibile:

1. ✅ **Fatto**: Frontend su Aruba
2. 🔜 **Prossimo**: Backend su Railway
3. 🔜 **Finale**: Collegare frontend → backend

---

## 💡 Note Finali

- Il frontend mostrerà la **landing page** ✅
- Login, dashboard, questionari **NON funzioneranno** fino a Railway ❌
- Questo è **NORMALE** e previsto
- Una volta configurato Railway, **TUTTO funzionerà** ✨

---

**Fatto? Dimmi quando vedi la landing page su Aruba!** 🚀
