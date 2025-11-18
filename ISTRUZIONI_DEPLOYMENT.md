# 📋 ISTRUZIONI DEPLOYMENT ARUBA - Passo per Passo

## ✅ COSA HAI GIÀ FATTO

- ✅ Scaricato cartella `dist/` sul tuo computer

---

## 📦 COSA DEVI SCARICARE ANCORA

Scarica questi 3 file da Replit (click destro → Download):

```
✅ dist/                  → Già scaricato
📥 package.json           → DA SCARICARE
📥 package-lock.json      → DA SCARICARE
📝 .env                   → DA CREARE (vedi sotto)
```

---

## 📝 CREARE IL FILE .env

**Sul tuo computer**, crea un nuovo file chiamato `.env` con questo contenuto:

```env
# DATABASE (da Neon PostgreSQL)
DATABASE_URL=postgresql://user:password@host.neon.tech/medaxis2?sslmode=require

# EMAIL (Aruba SMTP)
SMTP_HOST=smtps.aruba.it
SMTP_PORT=465
SMTP_USER=noreply@neurohrv.it
SMTP_PASS=la_tua_password_email
SMTP_FROM=noreply@neurohrv.it

# PAYPAL
PAYPAL_CLIENT_ID=il_tuo_client_id
PAYPAL_CLIENT_SECRET=il_tuo_secret
PAYPAL_MODE=live
PAYPAL_PRO_PLAN_ID=il_tuo_piano_id

# URL APPLICAZIONE
APP_URL=https://www.neurohrv.it/medaxis2

# CHIAVE SESSIONI (genera una stringa casuale lunga)
SESSION_SECRET=stringa_casuale_molto_lunga_e_sicura

# AMBIENTE
NODE_ENV=production
PORT=5000
```

**IMPORTANTE**: Sostituisci tutti i valori `il_tuo_...` con le credenziali reali!

---

## 📂 COSA AVRAI SUL TUO COMPUTER

Alla fine dovresti avere:

```
📁 Downloads/
  ├── 📁 dist/
  │   ├── 📁 public/
  │   └── 📄 index.js
  ├── 📄 package.json
  ├── 📄 package-lock.json
  └── 📄 .env
```

---

## 🚀 PROSSIMO PASSO (dopo aver scaricato tutto)

Carica questi file su Aruba in questa posizione:

```
/var/www/neurohrv.it/medaxis2/
├── public/              ← Contenuto di dist/public/
├── index.js             ← File dist/index.js
├── package.json
├── package-lock.json
└── .env
```

**Come caricare?** Via FTP/SFTP (FileZilla o simile)

**Dopo il caricamento:**
1. Connettiti via SSH al server Aruba
2. Vai nella cartella: `cd /var/www/neurohrv.it/medaxis2`
3. Installa dipendenze: `npm install --production`
4. Avvia app: `pm2 start index.js --name medaxis2`

---

## 📖 Guida Completa

Per istruzioni dettagliate, leggi: **DEPLOYMENT_ARUBA.md**
