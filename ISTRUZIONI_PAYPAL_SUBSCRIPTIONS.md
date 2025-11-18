# Istruzioni Setup PayPal Subscriptions per MedAxis²

## Panoramica
Questo documento spiega come configurare gli abbonamenti ricorrenti PayPal per MedAxis². Il sistema supporta due piani:
- **PRO Mensile**: €12.19/mese (IVA inclusa)
- **PRO Annuale**: €134.20/anno (IVA inclusa)

## Prerequisiti
- Account PayPal Business attivo
- Accesso al [PayPal Developer Dashboard](https://developer.paypal.com/dashboard/)
- Credenziali API (Client ID e Secret)

---

## PASSO 1: Creare i Piani di Abbonamento su PayPal

### 1.1 Accedi al Dashboard PayPal
1. Vai su [PayPal.com](https://www.paypal.com/it/business)
2. Accedi con il tuo account business
3. Vai su **Prodotti e servizi** → **Abbonamenti**

### 1.2 Crea Piano Mensile (€12.19/mese)
1. Clicca **Crea piano**
2. Compila i campi:
   - **Nome piano**: `MedAxis² PRO - Mensile`
   - **Descrizione**: `Abbonamento mensile alla piattaforma MedAxis² - Piano PRO con funzionalità complete`
   - **Tipo di fatturazione**: `Ricorrente`
   - **Frequenza**: `Mensile`
   - **Prezzo**: `€12.19 EUR`
   - **IVA**: Assicurati che l'IVA sia già inclusa nel prezzo
   - **Periodo di prova**: Nessuno (o imposta secondo le tue esigenze)
3. Clicca **Crea piano**
4. **IMPORTANTE**: Annota il **Plan ID** (formato `P-XXXXXXXXXXXX`)

### 1.3 Crea Piano Annuale (€134.20/anno)
1. Clicca **Crea piano**
2. Compila i campi:
   - **Nome piano**: `MedAxis² PRO - Annuale`
   - **Descrizione**: `Abbonamento annuale alla piattaforma MedAxis² - Piano PRO con funzionalità complete (risparmio del 8%)`
   - **Tipo di fatturazione**: `Ricorrente`
   - **Frequenza**: `Annuale`
   - **Prezzo**: `€134.20 EUR`
   - **IVA**: Assicurati che l'IVA sia già inclusa nel prezzo
   - **Periodo di prova**: Nessuno (o imposta secondo le tue esigenze)
3. Clicca **Crea piano**
4. **IMPORTANTE**: Annota il **Plan ID** (formato `P-YYYYYYYYYYYY`)

---

## PASSO 2: Configurare le Variabili d'Ambiente

### 2.1 File `.env` (Development)
Crea o aggiorna il file `.env` nella root del progetto:

```bash
# PAYPAL (Backend)
PAYPAL_CLIENT_ID=il_tuo_client_id_sandbox
PAYPAL_CLIENT_SECRET=il_tuo_secret_sandbox
PAYPAL_MODE=sandbox  # Usa 'sandbox' per test, 'live' per produzione

# Piano PRO Mensile - Plan ID da PayPal
PAYPAL_PRO_PLAN_ID_MONTHLY=P-XXXXXXXXXXXX

# Piano PRO Annuale - Plan ID da PayPal
PAYPAL_PRO_PLAN_ID_YEARLY=P-YYYYYYYYYYYY

# PAYPAL (Frontend - accessibili nel browser)
VITE_PAYPAL_PRO_PLAN_ID_MONTHLY=P-XXXXXXXXXXXX
VITE_PAYPAL_PRO_PLAN_ID_YEARLY=P-YYYYYYYYYYYY

# URL APPLICAZIONE
APP_URL=http://localhost:5000
```

### 2.2 Produzione (Replit Secrets)
Per produzione, aggiungi le stesse variabili come Secrets su Replit o nel tuo ambiente di deployment:

1. `PAYPAL_CLIENT_ID` - Client ID Live
2. `PAYPAL_CLIENT_SECRET` - Secret Live
3. `PAYPAL_MODE` - `live`
4. `PAYPAL_PRO_PLAN_ID_MONTHLY` - Plan ID Mensile (Live)
5. `PAYPAL_PRO_PLAN_ID_YEARLY` - Plan ID Annuale (Live)
6. `VITE_PAYPAL_PRO_PLAN_ID_MONTHLY` - Plan ID Mensile (Live)
7. `VITE_PAYPAL_PRO_PLAN_ID_YEARLY` - Plan ID Annuale (Live)
8. `APP_URL` - `https://www.neurohrv.it/medaxis2`

---

## PASSO 3: Testare in Sandbox

### 3.1 Creare Account di Test
1. Vai al [PayPal Sandbox](https://developer.paypal.com/dashboard/accounts)
2. Crea due account:
   - **Account Business**: Riceverà i pagamenti
   - **Account Personal**: Pagatore di test

### 3.2 Eseguire Test di Abbonamento
1. Avvia l'applicazione in locale: `npm run dev`
2. Registra un nuovo operatore
3. Vai su Dashboard → Clicca "Passa a PRO"
4. Seleziona piano (Mensile o Annuale)
5. Compila il form di billing
6. Clicca "Completa Abbonamento con PayPal"
7. Usa le credenziali del **Personal Test Account** per pagare
8. Verifica che:
   - L'account venga aggiornato a PRO
   - La data di scadenza sia corretta (1 mese o 1 anno)
   - Il `subscriptionPeriod` sia salvato correttamente
   - Il `paypalSubscriptionId` sia salvato

### 3.3 Verificare Subscription su PayPal
1. Accedi al Business Sandbox account
2. Vai su **Attività** → **Abbonamenti**
3. Verifica che la subscription sia **Attiva**

---

## PASSO 4: Deploy in Produzione

### 4.1 Attivare Account Live
1. Assicurati che il tuo account PayPal Business sia verificato
2. Ottieni le credenziali API Live dal [PayPal Dashboard](https://www.paypal.com/businessprofile/settings/api-access)

### 4.2 Creare Piani Live
Ripeti i passaggi del **PASSO 1** usando l'account Live (non Sandbox)

### 4.3 Configurare Produzione
1. Aggiorna le variabili d'ambiente con:
   - `PAYPAL_MODE=live`
   - Credenziali API Live
   - Plan ID Live

### 4.4 Deploy
1. Esegui build: `npm run build`
2. Deploy su Aruba seguendo [DEPLOYMENT_ARUBA.md](./DEPLOYMENT_ARUBA.md)

---

## PASSO 5: Webhook PayPal (Opzionale ma Raccomandato)

Per gestire eventi come cancellazioni o mancati pagamenti, configura i webhook:

### 5.1 Configurare Webhook
1. Vai al [PayPal Developer Dashboard](https://developer.paypal.com/dashboard/)
2. Vai su **Webhooks** → **Add Webhook**
3. Imposta URL: `https://www.neurohrv.it/medaxis2/api/paypal/webhook`
4. Seleziona eventi:
   - `BILLING.SUBSCRIPTION.ACTIVATED`
   - `BILLING.SUBSCRIPTION.CANCELLED`
   - `BILLING.SUBSCRIPTION.SUSPENDED`
   - `BILLING.SUBSCRIPTION.PAYMENT.FAILED`

### 5.2 Implementare Endpoint Webhook (TODO)
**Nota**: Attualmente il webhook non è implementato. Per implementarlo:

1. Crea endpoint `/api/paypal/webhook` in `server/routes.ts`
2. Verifica la firma del webhook PayPal
3. Gestisci gli eventi (cancellazione, fallimento pagamento, etc.)
4. Aggiorna stato subscription nel database

---

## Risoluzione Problemi

### Errore: "Plan ID not found"
- Verifica che i Plan ID siano corretti nel file `.env`
- Assicurati di usare Plan ID Sandbox per test e Live per produzione
- I Plan ID devono iniziare con `P-`

### Errore: "Subscription not active"
- Verifica che l'utente abbia completato il processo di approvazione su PayPal
- Controlla che il piano sia attivo nel Dashboard PayPal
- Verifica che `PAYPAL_MODE` corrisponda all'ambiente (sandbox vs live)

### L'abbonamento non si attiva dopo il pagamento
- Verifica che i parametri `subscription_id` e `ba_token` siano nell'URL di ritorno
- Controlla i log del server per errori durante l'attivazione
- Verifica che l'endpoint `/api/operators/activate-subscription` funzioni correttamente

### Subscription ID non salvato nel database
- Controlla che la migration del database sia stata eseguita correttamente
- Verifica che le colonne `subscriptionPeriod` e `paypalSubscriptionId` esistano nella tabella `operators`

---

## Note Importanti

1. **IVA**: I prezzi indicati (€12.19 e €134.20) includono già l'IVA italiana al 22%
2. **Rinnovo Automatico**: Gli abbonamenti si rinnovano automaticamente ogni mese/anno
3. **Cancellazione**: Gli utenti possono cancellare l'abbonamento dal loro account PayPal
4. **Gestione Subscription**: Per vedere tutte le subscriptions attive, accedi al Dashboard PayPal Business
5. **Test**: Usa sempre l'ambiente Sandbox per test prima di andare in produzione

---

## Checklist Pre-Deploy

- [ ] Plan ID Mensile creato su PayPal Live
- [ ] Plan ID Annuale creato su PayPal Live
- [ ] Variabili d'ambiente configurate correttamente
- [ ] Test completato in Sandbox con successo
- [ ] Credenziali API Live ottenute
- [ ] `PAYPAL_MODE` impostato su `live`
- [ ] Build eseguito senza errori
- [ ] Database migration eseguita
- [ ] Webhook configurato (opzionale)

---

## Supporto

Per problemi tecnici o domande:
- Email: [Inserisci email supporto]
- Documentazione PayPal: https://developer.paypal.com/docs/subscriptions/
- PayPal Support: https://www.paypal.com/it/smarthelp/contact-us
