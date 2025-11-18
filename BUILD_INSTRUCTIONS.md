# 🔨 MedAxis² - Build Instructions per Produzione

Questa guida spiega come creare il build di produzione di MedAxis² pronto per il deployment su Aruba.

---

## 📋 Prerequisiti

- **Node.js**: v20+ installato
- **npm**: v10+ 
- **ImageMagick**: (opzionale) per generare icone PWA

Verifica versioni:
```bash
node --version   # Deve essere v20+
npm --version    # Deve essere v10+
```

---

## 🎨 STEP 1: Generare Icone PWA

Le icone sono necessarie per l'installazione della PWA su tutti i dispositivi.

### Opzione A: Script Automatico (Linux/Mac con ImageMagick)

```bash
# Rendi eseguibile lo script
chmod +x scripts/generate-pwa-icons.sh

# Esegui lo script
./scripts/generate-pwa-icons.sh
```

**⚠️ IMPORTANTE**: Le icone placeholder attuali sono copie di favicon.png. Per produzione, DEVI rigenerarle con dimensioni corrette!

Lo script genera automaticamente:
- `pwa-64x64.png`
- `pwa-192x192.png`
- `pwa-512x512.png`
- `apple-touch-icon-180x180.png`
- `maskable-icon-512x512.png`

### Opzione B: Tool Online

Se non hai ImageMagick, usa un tool online:

1. Vai su **https://www.pwabuilder.com/imageGenerator**
2. Carica `attached_assets/logo-medaxis2.png`
3. Scarica il pacchetto ZIP
4. Copia i file PNG in `client/public/` con i nomi corretti

### Opzione C: Manuale

Usa qualsiasi editor immagini (Photoshop, GIMP, Figma) per creare le 5 dimensioni richieste.

### Verifica Icone

```bash
ls -lh client/public/*.png

# Dovresti vedere:
# pwa-64x64.png
# pwa-192x192.png
# pwa-512x512.png
# apple-touch-icon-180x180.png
# maskable-icon-512x512.png
# favicon.png
# og-image.png
```

---

## ⚙️ STEP 2: Configurare Variabili d'Ambiente

Crea un file `.env` nella root del progetto con le variabili di produzione:

```bash
cat > .env << 'EOF'
# Database (Neon PostgreSQL)
DATABASE_URL=postgresql://user:password@host/database?sslmode=require

# Email (SMTP Aruba)
SMTP_HOST=smtp.aruba.it
SMTP_PORT=465
SMTP_USER=noreply@neurohrv.it
SMTP_PASS=your_smtp_password
SMTP_FROM=noreply@neurohrv.it

# PayPal (LIVE mode)
PAYPAL_CLIENT_ID=your_live_paypal_client_id
PAYPAL_CLIENT_SECRET=your_live_paypal_client_secret
PAYPAL_MODE=live

# Twilio WhatsApp (opzionale)
TWILIO_ACCOUNT_SID=your_twilio_sid
TWILIO_AUTH_TOKEN=your_twilio_token
TWILIO_WHATSAPP_FROM=whatsapp:+14155238886

# App Configuration
NODE_ENV=production
SESSION_SECRET=$(openssl rand -base64 64)
PORT=3000
BASE_URL=https://www.neurohrv.it/medaxis2
EOF
```

**⚠️ IMPORTANTE**: 
- Sostituisci TUTTI i placeholder con valori reali
- NON commitare `.env` su Git (già in .gitignore)
- Genera `SESSION_SECRET` casuale e unico

---

## 🔨 STEP 3: Build di Produzione

### 1. Installa Dipendenze

```bash
# Installa tutte le dipendenze (dev + prod)
npm install
```

### 2. Esegui Build

```bash
# Build frontend + backend
npm run build
```

Questo comando:
1. Compila TypeScript → JavaScript
2. Optimizza e minifica frontend (Vite)
3. Genera bundle produzione in `dist/`

### 3. Verifica Output

```bash
# Controlla struttura dist/
ls -R dist/

# Dovresti vedere:
# dist/
# ├── public/          ← Frontend statico
# │   ├── index.html
# │   ├── manifest.webmanifest
# │   ├── sw.js
# │   ├── assets/      ← CSS, JS chunks
# │   └── *.png        ← Icone PWA
# └── server/          ← Backend compilato (se configurato)
```

### 4. Test Build Locale (Opzionale)

Prima di uploadare su Aruba, testa il build localmente:

```bash
# Installa serve globalmente
npm install -g serve

# Serve frontend statico
serve -s dist/public -l 8080

# In altro terminale, avvia backend
cd dist/server
node index.js

# Apri browser: http://localhost:8080
```

---

## 📦 STEP 4: Preparare Pacchetto Deployment

### Struttura Pacchetto

```
medaxis2-deployment/
├── public/              # Da dist/public/
├── server/              # Da dist/server/ (o da root se non compilato)
├── node_modules/        # Solo produzione (da installare su server)
├── package.json
├── package-lock.json
├── .env                 # Variabili produzione
└── README.md            # Documentazione deployment
```

### Crea Pacchetto

```bash
# Crea directory deployment
mkdir -p ../medaxis2-deployment
cd ../medaxis2-deployment

# Copia build frontend
cp -r ../medaxis2/dist/public ./

# Copia backend
cp -r ../medaxis2/server ./
# O se hai compilato TypeScript:
# cp -r ../medaxis2/dist/server ./

# Copia configurazione
cp ../medaxis2/package.json ./
cp ../medaxis2/package-lock.json ./
cp ../medaxis2/.env ./

# Copia shared types se necessari
cp -r ../medaxis2/shared ./

# Lista file
ls -la
```

### Crea Archivio ZIP (Opzionale)

```bash
# Torna alla directory principale
cd ..

# Crea ZIP (esclude node_modules)
zip -r medaxis2-deployment.zip medaxis2-deployment -x "medaxis2-deployment/node_modules/*"

# Dimensione archivio
ls -lh medaxis2-deployment.zip
```

---

## 🚀 STEP 5: Upload su Aruba

Segui la **[Guida Deployment Aruba](./DEPLOYMENT_ARUBA.md)** completa per:

1. Upload file via FTP/SFTP
2. Configurazione web server (Apache/Nginx)
3. Installazione dipendenze produzione su server
4. Avvio backend con PM2 o systemd
5. Test installazione PWA

---

## 📊 Dimensioni Build Tipiche

Dimensioni approssimative del build ottimizzato:

```
dist/public/
├── index.html          ~5 KB
├── manifest.webmanifest ~2 KB
├── sw.js               ~4 KB
├── assets/
│   ├── index-*.js      ~500-800 KB (minified + gzip)
│   └── index-*.css     ~50-100 KB (minified + gzip)
└── *.png               ~50-200 KB totale

Totale frontend: ~1-2 MB (non compresso)
Totale backend: ~100-200 KB (TypeScript compilato)
node_modules: ~200-300 MB (solo produzione)
```

---

## 🔧 Troubleshooting Build

### Errore: "Module not found"
**Soluzione**: 
```bash
rm -rf node_modules package-lock.json
npm install
npm run build
```

### Errore: TypeScript compilation errors
**Soluzione**:
```bash
# Controlla errori TypeScript
npx tsc --noEmit

# Fix errori e ri-build
npm run build
```

### Errore: "Out of memory"
**Soluzione**:
```bash
# Aumenta memoria Node.js
export NODE_OPTIONS="--max-old-space-size=4096"
npm run build
```

### Build troppo lento
**Soluzione**:
```bash
# Build senza source maps (più veloce)
npm run build -- --mode production --sourcemap false
```

---

## 🔄 Build per Ambienti Diversi

### Staging Environment

```bash
# .env.staging
DATABASE_URL=postgresql://staging-db
PAYPAL_MODE=sandbox
BASE_URL=https://staging.neurohrv.it/medaxis2

# Build
NODE_ENV=staging npm run build
```

### Development Build

```bash
# Già configurato con npm run dev
npm run dev

# Build development (con source maps)
npm run build -- --mode development
```

---

## 📈 Ottimizzazioni Build

### 1. Code Splitting

Vite già fa code splitting automatico. Verifica in `dist/public/assets/`:
```bash
ls -lh dist/public/assets/

# Dovresti vedere multiple chunks:
# index-abc123.js
# vendor-def456.js
# chunk-ghi789.js
```

### 2. Tree Shaking

Rimuovi import non usati per ridurre bundle size:
```typescript
// ❌ BAD - importa tutto
import * as Icons from 'lucide-react'

// ✅ GOOD - importa solo ciò che serve
import { Mail, User, Settings } from 'lucide-react'
```

### 3. Lazy Loading

Componenti lazy-loaded riducono initial bundle:
```typescript
// Lazy load pagine non critiche
const Analytics = lazy(() => import('./pages/Analytics'))
```

### 4. Comprimi Immagini

```bash
# Ottimizza PNG
for file in client/public/*.png; do
  pngquant --quality=80-90 --ext .png --force "$file"
done

# O usa tool online: tinypng.com, squoosh.app
```

---

## ✅ Checklist Build Finale

Prima di uploadare su Aruba:

- [ ] Icone PWA generate (5 dimensioni)
- [ ] `.env` produzione configurato
- [ ] `npm run build` eseguito senza errori
- [ ] `dist/public/` contiene tutti gli asset
- [ ] `manifest.webmanifest` presente
- [ ] `sw.js` presente
- [ ] `NODE_ENV=production` in `.env`
- [ ] `PAYPAL_MODE=live` (non sandbox!)
- [ ] `SESSION_SECRET` generato casuale
- [ ] Database URL produzione corretto
- [ ] SMTP credenziali Aruba corrette
- [ ] Test build locale superato
- [ ] Bundle size ragionevole (<2MB gzipped)

---

## 🎯 Performance Target

Obiettivi Lighthouse per build produzione:

- **Performance**: 90+ / 100
- **PWA**: 100 / 100
- **Accessibility**: 95+ / 100
- **Best Practices**: 95+ / 100
- **SEO**: 100 / 100

Test con:
```bash
# Chrome DevTools → Lighthouse
# O CLI:
npm install -g @lhci/cli
lhci autorun --collect.url=https://www.neurohrv.it/medaxis2/
```

---

## 📞 Support

Per problemi durante il build:
1. Controlla log errori completi
2. Verifica versioni Node.js/npm corrette
3. Pulisci cache: `rm -rf node_modules dist .vite`
4. Re-installa: `npm install`
5. Consulta DEPLOYMENT_ARUBA.md per deployment

---

## 🎉 Build Completato!

Il tuo build di produzione è pronto per il deployment su **www.neurohrv.it/medaxis2/**!

**Prossimo Step**: Segui [DEPLOYMENT_ARUBA.md](./DEPLOYMENT_ARUBA.md) per caricare su server Aruba.

Buon deployment! 🚀
