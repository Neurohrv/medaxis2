# 🚀 Guida Deployment MedAxis² su Aruba Cloud
## www.neurohrv.it/medaxis2/

Questa guida completa spiega come deployare MedAxis² come Progressive Web App (PWA) sul tuo cloud Aruba.

---

## 📋 Prerequisiti

### 1. Server Requirements
- **Web Server**: Apache 2.4+ o Nginx 1.18+
- **HTTPS**: Certificato SSL/TLS attivo (obbligatorio per PWA)
- **Node.js**: v20+ (per build locale)
- **FTP/SFTP**: Accesso al server Aruba

### 2. Variabili d'Ambiente Necessarie
Prima del deployment, configura le seguenti variabili sul server:

```bash
# Database (Neon PostgreSQL)
DATABASE_URL=postgresql://user:password@host/database

# Email (SMTP)
SMTP_HOST=smtp.aruba.it
SMTP_PORT=465
SMTP_USER=noreply@neurohrv.it
SMTP_PASS=your_smtp_password
SMTP_FROM=noreply@neurohrv.it

# PayPal
PAYPAL_CLIENT_ID=your_paypal_client_id
PAYPAL_CLIENT_SECRET=your_paypal_client_secret
PAYPAL_MODE=live  # o 'sandbox' per test

# Twilio (WhatsApp - opzionale)
TWILIO_ACCOUNT_SID=your_twilio_sid
TWILIO_AUTH_TOKEN=your_twilio_token
TWILIO_WHATSAPP_FROM=whatsapp:+14155238886

# App Configuration
NODE_ENV=production
SESSION_SECRET=genera_una_stringa_casuale_lunga_e_sicura_almeno_64_caratteri
PORT=3000
BASE_URL=https://www.neurohrv.it/medaxis2
```

---

## 🎨 STEP 1: Generare le Icone PWA

Le icone PWA sono necessarie per l'installazione su Android, iOS, Windows e Mac.

### ⚠️ CRITICAL - Icone Placeholder Esistenti
**Le icone PWA attuali in `client/public/` sono PLACEHOLDER** (copie di favicon.png).
**DEVI rigenerarle con dimensioni corrette prima del deployment produzione!**

### Opzione A: Tool Online (RACCOMANDATO - Più Facile)
1. Vai su **https://realfavicongenerator.net** o **https://www.pwabuilder.com/imageGenerator**
2. Carica il file `attached_assets/logo-medaxis2.png`
3. Configura:
   - **Android**: Background #0284c7 (blu MedAxis²)
   - **iOS**: Non aggiungere margini
   - **Windows**: Tile color #0284c7
4. Scarica il pacchetto ZIP generato
5. Estrai e rinomina i file come segue:
   - `android-chrome-192x192.png` → `pwa-192x192.png`
   - `android-chrome-512x512.png` → `pwa-512x512.png`
   - `apple-touch-icon.png` → `apple-touch-icon-180x180.png`
   - Crea `maskable-icon-512x512.png` (versione con margini del logo)
   - `favicon-32x32.png` → `pwa-64x64.png` (ridimensiona a 64x64)

### Opzione B: ImageMagick (Linux/Mac)
```bash
cd attached_assets

# Genera icone PWA
convert logo-medaxis2.png -resize 64x64 pwa-64x64.png
convert logo-medaxis2.png -resize 192x192 pwa-192x192.png
convert logo-medaxis2.png -resize 512x512 pwa-512x512.png
convert logo-medaxis2.png -resize 180x180 apple-touch-icon-180x180.png

# Maskable icon (con padding 20%)
convert logo-medaxis2.png -resize 410x410 -gravity center -extent 512x512 -background "#0284c7" maskable-icon-512x512.png

# Sposta le icone in client/public/
mv pwa-*.png apple-touch-icon-*.png maskable-icon-*.png ../client/public/
```

### Dove Mettere le Icone
Copia TUTTI i file icona in `client/public/`:
```
client/public/
├── pwa-64x64.png
├── pwa-192x192.png
├── pwa-512x512.png
├── maskable-icon-512x512.png
└── apple-touch-icon-180x180.png
```

---

## 🔨 STEP 2: Build di Produzione

### 1. Prepara l'Ambiente Locale
```bash
# Clona il progetto o usa il codice esistente
cd /path/to/medaxis2

# Installa dipendenze
npm install

# Crea file .env per build
cat > .env << 'EOF'
DATABASE_URL=postgresql://user:password@host/database
SMTP_HOST=smtp.aruba.it
SMTP_PORT=465
SMTP_USER=noreply@neurohrv.it
SMTP_PASS=your_smtp_password
SMTP_FROM=noreply@neurohrv.it
PAYPAL_CLIENT_ID=your_paypal_client_id
PAYPAL_CLIENT_SECRET=your_paypal_client_secret
PAYPAL_MODE=live
NODE_ENV=production
SESSION_SECRET=your_session_secret
BASE_URL=https://www.neurohrv.it/medaxis2
EOF
```

### 2. Esegui Build
```bash
# Build frontend (genera dist/public/)
npm run build

# Build backend (se necessario - TypeScript to JavaScript)
npx tsc -p tsconfig.json --outDir dist/server
```

### 3. Verifica Output Build
```bash
ls -la dist/
# Dovresti vedere:
# dist/public/   - Frontend statico (HTML, CSS, JS, icone)
# dist/server/   - Backend Node.js compilato
```

---

## 📤 STEP 3: Upload su Aruba

### Struttura File su Server
Carica i file sul server Aruba nella seguente struttura:
```
/var/www/neurohrv.it/medaxis2/
├── public/              # Frontend statico (da dist/public/)
│   ├── index.html
│   ├── manifest.webmanifest
│   ├── sw.js
│   ├── pwa-192x192.png
│   ├── pwa-512x512.png
│   ├── apple-touch-icon-180x180.png
│   ├── maskable-icon-512x512.png
│   ├── assets/         # CSS, JS chunks
│   └── ...
├── server/              # Backend Node.js (da dist/server/)
│   ├── index.js
│   ├── routes.js
│   ├── storage.js
│   └── ...
├── node_modules/        # Dipendenze produzione
├── package.json
├── .env                 # Variabili d'ambiente (NON commitare!)
└── ...
```

### Upload via FTP/SFTP
```bash
# Opzione 1: FileZilla (GUI)
# 1. Connetti a ftp.neurohrv.it
# 2. Naviga a /var/www/neurohrv.it/medaxis2/
# 3. Carica dist/public/ → public/
# 4. Carica dist/server/ → server/
# 5. Carica package.json, .env

# Opzione 2: SFTP Command Line
sftp user@neurohrv.it
cd /var/www/neurohrv.it/medaxis2
put -r dist/public/* public/
put -r dist/server/* server/
put package.json
put .env
exit

# Opzione 3: rsync (più veloce)
rsync -avz --progress dist/public/ user@neurohrv.it:/var/www/neurohrv.it/medaxis2/public/
rsync -avz --progress dist/server/ user@neurohrv.it:/var/www/neurohrv.it/medaxis2/server/
```

### Installa Dipendenze Produzione su Server
```bash
# SSH al server
ssh user@neurohrv.it

# Vai nella directory
cd /var/www/neurohrv.it/medaxis2

# Installa solo dipendenze produzione
npm install --production

# Verifica
ls node_modules/
```

---

## ⚙️ STEP 4: Configurazione Web Server

### Opzione A: Apache (Raccomandato per Aruba)

Crea/modifica `.htaccess` in `/var/www/neurohrv.it/medaxis2/public/`:

```apache
# .htaccess per MedAxis² PWA
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /medaxis2/
  
  # HTTPS Redirect (obbligatorio per PWA)
  RewriteCond %{HTTPS} off
  RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
  
  # API Proxy - Inoltra richieste /api/* al backend Node.js
  RewriteCond %{REQUEST_URI} ^/medaxis2/api/
  RewriteRule ^api/(.*)$ http://localhost:3000/api/$1 [P,L]
  
  # Service Worker - Serve sempre dalla root
  RewriteRule ^sw\.js$ /medaxis2/sw.js [L]
  RewriteRule ^manifest\.webmanifest$ /medaxis2/manifest.webmanifest [L]
  
  # SPA Routing - Reindirizza tutto al index.html (tranne file esistenti)
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule ^(.*)$ /medaxis2/index.html [L]
</IfModule>

# Cache Headers per PWA
<IfModule mod_headers.c>
  # Service Worker - No cache
  <FilesMatch "sw\.js$">
    Header set Cache-Control "no-cache, no-store, must-revalidate"
    Header set Pragma "no-cache"
    Header set Expires 0
  </FilesMatch>
  
  # Manifest - Short cache
  <FilesMatch "manifest\.webmanifest$">
    Header set Cache-Control "public, max-age=3600"
  </FilesMatch>
  
  # Static Assets - Long cache
  <FilesMatch "\.(jpg|jpeg|png|gif|ico|svg|woff|woff2|ttf|eot|css|js)$">
    Header set Cache-Control "public, max-age=31536000, immutable"
  </FilesMatch>
</IfModule>

# Compressione GZIP
<IfModule mod_deflate.c>
  AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript application/javascript application/json application/xml
</IfModule>

# Sicurezza Headers
<IfModule mod_headers.c>
  Header set X-Content-Type-Options "nosniff"
  Header set X-Frame-Options "SAMEORIGIN"
  Header set X-XSS-Protection "1; mode=block"
  Header set Referrer-Policy "strict-origin-when-cross-origin"
</IfModule>
```

### Opzione B: Nginx

Se usi Nginx, crea/modifica `/etc/nginx/sites-available/neurohrv.it`:

```nginx
server {
    listen 443 ssl http2;
    server_name www.neurohrv.it;
    
    # SSL Configuration
    ssl_certificate /etc/letsencrypt/live/neurohrv.it/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/neurohrv.it/privkey.pem;
    
    # Root e index
    root /var/www/neurohrv.it/medaxis2/public;
    index index.html;
    
    # Location per /medaxis2/
    location /medaxis2/ {
        alias /var/www/neurohrv.it/medaxis2/public/;
        try_files $uri $uri/ /medaxis2/index.html;
        
        # Cache headers per static assets
        location ~* \.(jpg|jpeg|png|gif|ico|svg|woff|woff2|ttf|eot|css|js)$ {
            expires 1y;
            add_header Cache-Control "public, immutable";
        }
        
        # No cache per Service Worker
        location ~* sw\.js$ {
            add_header Cache-Control "no-cache, no-store, must-revalidate";
            add_header Pragma "no-cache";
            add_header Expires 0;
        }
    }
    
    # API Proxy - Inoltra a Node.js backend
    location /medaxis2/api/ {
        proxy_pass http://localhost:3000/api/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
    
    # Security Headers
    add_header X-Content-Type-Options "nosniff";
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header Referrer-Policy "strict-origin-when-cross-origin";
    
    # Gzip
    gzip on;
    gzip_vary on;
    gzip_types text/plain text/css text/xml text/javascript application/json application/javascript application/xml+rss;
}

# HTTP to HTTPS redirect
server {
    listen 80;
    server_name www.neurohrv.it;
    return 301 https://$server_name$request_uri;
}
```

Poi ricarica Nginx:
```bash
sudo nginx -t  # Testa configurazione
sudo systemctl reload nginx
```

---

## 🚀 STEP 5: Avviare Backend Node.js

### Opzione A: PM2 (Raccomandato)
```bash
# Installa PM2 globalmente
npm install -g pm2

# Avvia l'applicazione
cd /var/www/neurohrv.it/medaxis2
pm2 start server/index.js --name medaxis2 --env production

# Configura auto-start al riavvio server
pm2 startup
pm2 save

# Monitoraggio
pm2 status
pm2 logs medaxis2
pm2 monit
```

### Opzione B: Systemd Service
Crea `/etc/systemd/system/medaxis2.service`:

```ini
[Unit]
Description=MedAxis² Backend Server
After=network.target

[Service]
Type=simple
User=www-data
WorkingDirectory=/var/www/neurohrv.it/medaxis2
Environment="NODE_ENV=production"
EnvironmentFile=/var/www/neurohrv.it/medaxis2/.env
ExecStart=/usr/bin/node server/index.js
Restart=on-failure
RestartSec=10
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=medaxis2

[Install]
WantedBy=multi-user.target
```

Attiva il service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable medaxis2
sudo systemctl start medaxis2
sudo systemctl status medaxis2

# Logs
sudo journalctl -u medaxis2 -f
```

---

## ✅ STEP 6: Verifica Deployment

### 1. Test Accesso Web
Apri browser e vai a:
```
https://www.neurohrv.it/medaxis2/
```

Dovresti vedere la landing page di MedAxis².

### 2. Test PWA Manifest
```
https://www.neurohrv.it/medaxis2/manifest.webmanifest
```

Dovresti vedere il JSON del manifest.

### 3. Test Service Worker
Apri DevTools (F12) → Application → Service Workers
- Dovresti vedere "sw.js" registrato e attivo

### 4. Test Installazione PWA

**Android (Chrome):**
1. Apri https://www.neurohrv.it/medaxis2/ su Chrome
2. Tocca i 3 puntini → "Installa app" o "Aggiungi a schermata Home"
3. L'app si installa come app nativa

**iOS (Safari):**
1. Apri https://www.neurohrv.it/medaxis2/ su Safari
2. Tocca l'icona Condividi
3. Seleziona "Aggiungi a Home"
4. L'app appare nella Home Screen

**Windows/Mac (Chrome/Edge):**
1. Apri https://www.neurohrv.it/medaxis2/
2. Guarda l'icona "+" nella barra indirizzi
3. Clicca "Installa MedAxis²"
4. L'app si apre in finestra dedicata

### 5. Test Lighthouse PWA
In Chrome DevTools (F12) → Lighthouse:
```
☐ Performance
☑ Progressive Web App
☐ Best Practices
☐ Accessibility
☐ SEO

Run Audit
```

**Target Score**: PWA 100/100

---

## 🔧 Troubleshooting

### Problema: Service Worker non si registra
**Soluzione:**
1. Verifica HTTPS attivo (obbligatorio!)
2. Controlla console browser per errori
3. Verifica path `/sw.js` accessibile
4. Cancella cache browser (Ctrl+Shift+Delete)

### Problema: Icone PWA mancanti
**Soluzione:**
1. Verifica presenza file in `public/`:
   ```bash
   ls -la public/pwa-*.png public/apple-touch-icon-*.png
   ```
2. Controlla che i path nel manifest siano corretti
3. Testa accesso diretto: `https://www.neurohrv.it/medaxis2/pwa-192x192.png`

### Problema: API non funzionano
**Soluzione:**
1. Verifica backend Node.js in esecuzione: `pm2 status`
2. Controlla logs: `pm2 logs medaxis2`
3. Testa API direttamente: `curl https://www.neurohrv.it/medaxis2/api/health`
4. Verifica proxy Apache/Nginx configurato correttamente

### Problema: "Non installabile" su Android/iOS
**Soluzione:**
1. Lighthouse PWA audit deve essere 100/100
2. Manifest deve avere almeno icona 192x192 e 512x512
3. HTTPS obbligatorio
4. Service Worker deve essere registrato
5. `start_url` deve corrispondere alla base URL

### Problema: Database connection failed
**Soluzione:**
1. Verifica `DATABASE_URL` in `.env`
2. Testa connessione da server:
   ```bash
   psql "postgresql://user:pass@host/db"
   ```
3. Controlla firewall/whitelist IP server su Neon
4. Verifica credenziali database Neon

---

## 🔄 Aggiornamenti Futuri

### Deploy Nuova Versione
```bash
# 1. Build locale
npm run build

# 2. Upload solo file modificati
rsync -avz --progress --delete dist/public/ user@neurohrv.it:/var/www/neurohrv.it/medaxis2/public/
rsync -avz --progress dist/server/ user@neurohrv.it:/var/www/neurohrv.it/medaxis2/server/

# 3. Riavvia backend
ssh user@neurohrv.it
pm2 restart medaxis2
```

### Incrementa Versione Cache
Dopo modifiche sostanziali, aggiorna `CACHE_NAME` in `sw.js`:
```javascript
const CACHE_NAME = 'medaxis2-v1.0.1'; // Incrementa versione
```

Questo forza il browser a ricaricare tutti gli asset cachati.

---

## 📊 Monitoraggio Produzione

### Logs
```bash
# Backend logs (PM2)
pm2 logs medaxis2 --lines 100

# Apache logs
tail -f /var/log/apache2/error.log
tail -f /var/log/apache2/access.log

# Nginx logs
tail -f /var/log/nginx/error.log
tail -f /var/log/nginx/access.log
```

### Performance Monitoring
- **Uptime**: Usa PM2 o servizio esterno (UptimeRobot, Pingdom)
- **Analytics**: Integra Google Analytics o Plausible
- **Error Tracking**: Sentry.io per errori JavaScript

---

## 🔒 Sicurezza Best Practices

1. **HTTPS Obbligatorio**: Renew SSL cert prima scadenza
   ```bash
   # Let's Encrypt auto-renewal
   sudo certbot renew --dry-run
   ```

2. **Firewall**: Apri solo porte necessarie (80, 443, 3000 interno)
3. **Backup Database**: Automatizza backup giornalieri Neon
4. **Secrets**: Mai commitare `.env` su Git
5. **Updates**: Mantieni Node.js e dipendenze aggiornate
   ```bash
   npm audit
   npm audit fix
   ```

---

## 📱 Test Dispositivi

Prima del rilascio ufficiale, testa su:
- ✅ Android (Chrome, Samsung Internet, Firefox)
- ✅ iOS (Safari, Chrome iOS)
- ✅ Windows (Chrome, Edge, Firefox)
- ✅ macOS (Safari, Chrome, Firefox)
- ✅ Tablet Android/iPad

---

## 🎯 Checklist Deployment Finale

Prima di annunciare il lancio:

- [ ] HTTPS attivo e certificato valido
- [ ] Tutte le icone PWA presenti e corrette
- [ ] Service Worker registrato senza errori
- [ ] Manifest accessibile
- [ ] Lighthouse PWA score 100/100
- [ ] Backend Node.js avviato con PM2/systemd
- [ ] Database connesso e funzionante
- [ ] Email SMTP configurato e testato
- [ ] PayPal configurato (modalità LIVE)
- [ ] Test registrazione nuovo operatore
- [ ] Test invio questionario paziente
- [ ] Test generazione PDF report
- [ ] Test installazione PWA su Android
- [ ] Test installazione PWA su iOS
- [ ] Test installazione PWA su Windows/Mac
- [ ] Backup database configurato
- [ ] Monitoring/logs configurato
- [ ] Documentazione aggiornata

---

## 📞 Supporto

Per problemi tecnici durante il deployment:
1. Controlla logs backend e web server
2. Verifica Lighthouse PWA audit
3. Consulta Chrome DevTools → Application → Service Workers
4. Riferisciti a questa guida per troubleshooting

**Contatto Sviluppatore**: [Tuo contatto supporto]

---

## 🎉 Congratulazioni!

MedAxis² è ora live come Progressive Web App professionale su **www.neurohrv.it/medaxis2/**!

Gli utenti possono installare l'app su qualsiasi dispositivo per un'esperienza nativa completa.

**Prossimi Step**:
- Marketing e comunicazione lancio
- Onboarding primi operatori
- Monitoring performance e feedback
- Iterazioni e miglioramenti continui

Buon lavoro! 🚀
