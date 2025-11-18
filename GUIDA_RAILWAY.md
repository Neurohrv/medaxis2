# 🚂 Guida Completa: Deploy MedAxis² su Railway

## 📋 Preparazione

### 1. Account Necessari
- ✅ Account GitHub (https://github.com/signup)
- ✅ Account Railway (https://railway.app - Login con GitHub)

---

## 🔧 STEP 1: Preparare il Codice per Railway

### Su Replit (qui):

1. **Sostituire vite.config.ts**
   ```bash
   # Rinomina quello vecchio
   mv vite.config.ts vite.config.replit.ts
   # Usa quello per Railway
   mv vite.config.railway.ts vite.config.ts
   ```

2. **I file `railway.json` e `.gitignore` sono già pronti!**

---

## 📤 STEP 2: Caricare su GitHub

### Opzione A: Da Replit (più facile)

1. Apri la **Shell** in basso su Replit
2. Esegui questi comandi UNO alla volta:

```bash
# Configura Git (sostituisci con il TUO nome e email)
git config --global user.name "Il Tuo Nome"
git config --global user.email "tua@email.com"

# Inizializza repository
git init
git add .
git commit -m "Prima versione MedAxis² per Railway"

# Collega a GitHub (sostituisci USERNAME con il tuo)
git remote add origin https://github.com/USERNAME/medaxis2.git
git branch -M main
git push -u origin main
```

### Opzione B: Download + Upload Manuale

1. Su Replit: Scarica tutti i file (Download as ZIP)
2. Estrai lo ZIP sul tuo computer
3. Su GitHub: Clicca "uploading an existing file"
4. Trascina tutti i file estratti

---

## 🚀 STEP 3: Deploy su Railway

### 3.1 - Creare Nuovo Progetto

1. Vai su https://railway.app
2. Clicca **"New Project"**
3. Seleziona **"Deploy from GitHub repo"**
4. Autorizza Railway ad accedere a GitHub
5. Seleziona il repository **"medaxis2"**

### 3.2 - Aggiungere Database PostgreSQL

1. Nel progetto Railway, clicca **"+ New"**
2. Seleziona **"Database" → "PostgreSQL"**
3. Railway crea automaticamente il database
4. Copia la **DATABASE_URL** (la usiamo dopo)

### 3.3 - Configurare Variabili d'Ambiente

1. Clicca sul servizio **medaxis2** (non il database)
2. Vai su **"Variables"** tab
3. Clicca **"New Variable"**
4. Aggiungi TUTTE queste variabili:

```bash
# Database (copia da PostgreSQL service)
DATABASE_URL=postgresql://...

# App Settings
NODE_ENV=production
PORT=5000
SESSION_SECRET=genera-una-stringa-casuale-lunga-almeno-32-caratteri

# PayPal (copia da Replit)
PAYPAL_CLIENT_ID=il-tuo-client-id
PAYPAL_CLIENT_SECRET=il-tuo-secret
PAYPAL_PRO_PLAN_ID_MONTHLY=piano-mensile-id
PAYPAL_PRO_PLAN_ID_YEARLY=piano-annuale-id
VITE_PAYPAL_CLIENT_ID=il-tuo-client-id

# Email SMTP (copia da Replit)
SMTP_HOST=smtp.tuo-provider.com
SMTP_PORT=587
SMTP_USER=tuo-user
SMTP_PASS=tua-password
SMTP_FROM=noreply@tuodominio.com

# Optional: Twilio WhatsApp (se lo usi)
TWILIO_ACCOUNT_SID=tuo-sid
TWILIO_AUTH_TOKEN=tuo-token
TWILIO_PHONE_NUMBER=+39...
```

**🔑 IMPORTANTE**: Per trovare i valori su Replit:
- Vai su **"Secrets"** (icona lucchetto) su Replit
- Copia ogni valore e incollalo su Railway

### 3.4 - Deploy!

1. Railway inizia automaticamente il deploy
2. Guarda i logs (tab "Deploy")
3. Aspetta il messaggio **"Build successful"**
4. Clicca su **"Deployments"** per vedere l'URL

---

## 🌐 STEP 4: Configurare Dominio Custom

### 4.1 - Generare Dominio Railway

1. Nel servizio, vai su **"Settings"**
2. Sezione **"Networking"**
3. Clicca **"Generate Domain"**
4. Railway crea un URL tipo `medaxis2-production.up.railway.app`

### 4.2 - Usare il Tuo Dominio (www.neurohrv.it)

1. Su Railway: **Settings → Networking → Custom Domain**
2. Inserisci: `www.neurohrv.it`
3. Railway ti dà un **CNAME record** (tipo: `xyz.railway.app`)

4. **Su Aruba** (pannello DNS):
   - Tipo: **CNAME**
   - Nome: **www**
   - Valore: **quello fornito da Railway** (`xyz.railway.app`)
   - TTL: 3600

5. **Aspetta 5-30 minuti** per propagazione DNS
6. Testa: apri `https://www.neurohrv.it`

### 4.3 - Aggiornare APP_URL

Su Railway, aggiungi/modifica variabile:
```
APP_URL=https://www.neurohrv.it
```

---

## ✅ STEP 5: Verificare Funzionamento

1. **Apri l'URL** del tuo sito
2. **Prova login** con admin
3. **Crea un questionario** di test
4. **Controlla email** (verifica SMTP)
5. **Controlla database** (dati salvati?)

---

## 💰 Monitoraggio Costi

1. Dashboard Railway → **"Usage"**
2. Vedi crediti rimanenti ($5 gratis iniziali)
3. Imposta **Budget Alert** (Settings → Budget)
   - Consiglio: $15/mese inizialmente

---

## 🔄 Aggiornamenti Futuri

Quando fai modifiche al codice:

```bash
# Su Replit o sul tuo computer
git add .
git commit -m "Descrizione modifiche"
git push

# Railway rileva automaticamente e ri-deploya!
```

---

## 🆘 Problemi Comuni

### ❌ Build Failed
- Controlla logs Railway
- Verifica che tutte le variabili d'ambiente siano settate
- DATABASE_URL deve essere corretto

### ❌ App Non Parte
- Controlla **PORT=5000** sia settato
- Verifica DATABASE_URL valido
- Guarda i logs runtime

### ❌ Database Errori
- Railway PostgreSQL deve essere nello stesso progetto
- DATABASE_URL formato: `postgresql://user:pass@host:5432/dbname`

### ❌ Email Non Funzionano
- Verifica SMTP_* variabili corrette
- Controlla che SMTP_PORT sia numero (587 o 465)

---

## 📊 Costi Stimati

- **Primi giorni**: GRATIS ($5 crediti)
- **Mese 1**: ~$10-15 (traffico basso)
- **Mese standard**: ~$15-20 (traffico medio)

**Nota**: Railway fattura a consumo. Monitora su Dashboard!

---

## ✨ Vantaggi vs Replit

✅ **~$10/mese risparmiati**
✅ Deploy automatico da GitHub
✅ Logs migliori
✅ Database incluso gratis
✅ Scaling automatico
✅ Nessun "sleep" dell'app

---

Hai problemi? Scrivimi e ti aiuto! 🚀
