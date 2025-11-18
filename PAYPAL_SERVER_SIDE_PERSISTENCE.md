# PayPal Subscription - Server-Side Plan Type Persistence

## Sommario delle Modifiche

Il sistema di abbonamento PayPal è stato aggiornato per salvare il tipo di piano (monthly/yearly) **server-side** in un database dedicato, risolvendo i problemi di sincronizzazione cross-device e URL legacy.

---

## 🎯 Problema Risolto

**Problema precedente**: Il tipo di piano veniva passato tramite URL query parameters (`?planType=monthly`), causando:
- ❌ Perdita di informazioni se l'utente approvava su un dispositivo diverso
- ❌ Impossibilità di determinare il piano se l'URL veniva modificato o condiviso
- ❌ Dipendenza da localStorage/URL non affidabile

**Soluzione attuale**: Il tipo di piano viene salvato **nel database** al momento della creazione della subscription PayPal, garantendo:
- ✅ Affidabilità totale indipendentemente dal dispositivo
- ✅ Tracciabilità completa di ogni subscription creata
- ✅ Fallback automatico tramite PayPal API se necessario

---

## 🏗️ Architettura

### Database Schema

**Nuova tabella `pending_subscriptions`:**

```typescript
{
  id: varchar (UUID),
  paypalSubscriptionId: text (unique),
  planType: varchar ('monthly' | 'yearly'),
  operatorId: varchar (FK -> operators.id),
  createdAt: timestamp,
  expiresAt: timestamp (auto-cleanup dopo 24 ore)
}
```

### Flusso di Subscription

```
1. User selects plan (monthly/yearly)
   ↓
2. Frontend calls POST /paypal/subscription
   ↓
3. Backend:
   - Creates PayPal subscription
   - Saves to pending_subscriptions table:
     { paypalSubscriptionId, planType, operatorId }
   ↓
4. PayPal redirects to /dashboard?subscription_id=I-XXX&ba_token=YYY
   ↓
5. Frontend useEffect detects subscription_id
   ↓
6. Frontend calls POST /api/operators/activate-subscription
   ↓
7. Backend:
   - Looks up planType from pending_subscriptions
   - Validates subscription with PayPal
   - Updates operator to PRO
   - Deletes pending record
   ↓
8. ✅ Subscription activated successfully
```

### Fallback Logic

Se il record pending non esiste (edge case):
1. Backend chiama PayPal API per recuperare subscription details
2. Confronta `plan_id` con `PAYPAL_PRO_PLAN_ID_MONTHLY` / `PAYPAL_PRO_PLAN_ID_YEARLY`
3. Determina planType automaticamente
4. Default a "monthly" se tutto fallisce

---

## 🔐 Sicurezza

### Route Authentication

```typescript
// server/routes.ts
app.post("/paypal/subscription", isAuthenticated, ...);
app.get("/paypal/subscription/:subscriptionId", isAuthenticated, ...);
app.post("/api/operators/activate-subscription", isAuthenticated, ...);
```

### Operator Lookup

```typescript
// server/paypal.ts
const operator = await storage.getOperatorByUserId(req.user.id);
// Usa l'operatorId corretto dal database
```

---

## 📝 Modifiche ai File

### Backend

1. **shared/schema.ts**
   - Aggiunta tabella `pendingSubscriptions` con TTL 24 ore

2. **server/paypal.ts**
   - `createPaypalSubscription`: Salva pending record dopo creazione PayPal
   - Usa `storage.getOperatorByUserId()` invece di query diretta
   - Rimuove `planType` dalla `return_url`

3. **server/routes.ts**
   - Aggiunge `isAuthenticated` middleware a route PayPal
   - `/api/operators/activate-subscription`:
     - Legge `planType` da `pendingSubscriptions`
     - Fallback a PayPal API se necessario
     - Cleanup automatico del record pending

### Frontend

4. **client/src/pages/OperatorDashboard.tsx**
   - useEffect semplificato: legge solo `subscription_id` da URL
   - Rimuove tutta la logica di fallback client-side per `planType`
   - Non invia più `planType` nel body di activate-subscription

### Documentazione

5. **replit.md**
   - Aggiornata sezione "Subscription & Payment"
   - Aggiornata sezione "Database"

---

## ✅ Vantaggi

### Affidabilità
- ✅ Plan type salvato server-side, non dipende da URL o localStorage
- ✅ Cross-device support completo
- ✅ Nessun rischio di manipolazione client-side

### Sicurezza
- ✅ Route autenticate, solo operatori loggati possono creare subscriptions
- ✅ Operator ID verificato server-side
- ✅ Validazione plan_id contro environment variables

### Manutenibilità
- ✅ Cleanup automatico record vecchi (TTL 24 ore)
- ✅ Fallback robusto se pending record mancante
- ✅ Logging completo per troubleshooting

### User Experience
- ✅ URL più puliti (no planType param)
- ✅ Nessuna dipendenza da localStorage
- ✅ Funziona anche se utente approva su dispositivo diverso

---

## 🧪 Testing

### Test Manuale

1. **Happy Path**
   - Login come operatore GO
   - Clicca "Passa a PRO"
   - Seleziona piano (Monthly o Yearly)
   - Approva su PayPal Sandbox
   - Verifica upgrade a PRO

2. **Cross-Device**
   - Inizia subscription su Desktop
   - Approva su Mobile
   - Verifica che il piano corretto venga attivato

3. **Fallback Path**
   - Cancella manualmente record pending dal database
   - Approva subscription
   - Verifica che il sistema determini il plan type da PayPal API

### Log da Verificare

```bash
# Creazione pending record
Pending subscription saved: I-XXXXXXXXXX (monthly) for operator abc-123

# Lookup da database
Found planType from database: monthly

# Fallback a PayPal API (se pending mancante)
No pending subscription found for I-XXX, falling back to PayPal API lookup
Determined planType from PayPal API: yearly

# Cleanup
Cleaned up pending subscription record for I-XXXXXXXXXX

# Activation success
Subscription activated for operator abc-123: monthly plan, expires 2025-12-16T...
```

---

## 📌 Note per Produzione

### Environment Variables Richieste

```bash
# Backend
PAYPAL_PRO_PLAN_ID_MONTHLY=P-XXXXXXXXXXXX
PAYPAL_PRO_PLAN_ID_YEARLY=P-YYYYYYYYYYYY

# Frontend
VITE_PAYPAL_PRO_PLAN_ID_MONTHLY=P-XXXXXXXXXXXX
VITE_PAYPAL_PRO_PLAN_ID_YEARLY=P-YYYYYYYYYYYY
```

### Monitoraggio

1. **Pending Records**: Verificare che i record vecchi vengano cleanup automaticamente
2. **Fallback Usage**: Monitorare quante volte il fallback PayPal API viene usato
3. **Activation Failures**: Log dettagliati per troubleshooting

### Operational Runbook

Se un utente segnala problemi di activation:
1. Verifica se esiste record in `pending_subscriptions` per il subscription_id
2. Se mancante, il sistema userà automaticamente fallback PayPal API
3. Verifica i logs backend per identificare il planType determinato
4. Se necessario, query manuale PayPal API per verificare subscription state

---

## 🚀 Deployment

### Database Migration

La migration è già stata eseguita con:

```bash
npm run db:push
```

### Rollout

1. ✅ Schema database aggiornato
2. ✅ Backend aggiornato con server-side persistence
3. ✅ Frontend semplificato per rimuovere dipendenze client-side
4. ✅ Documentation aggiornata

### Backward Compatibility

Il sistema mantiene cleanup di localStorage legacy per utenti che avevano pending subscriptions dal vecchio sistema:

```typescript
localStorage.removeItem("pendingSubscriptionPlanType");
```

---

## 📞 Support

Per domande o problemi:
- Controllare i logs backend per dettagli activation
- Verificare environment variables configurate correttamente
- Testare su PayPal Sandbox prima di produzione

---

**Ultimo aggiornamento**: 16 Novembre 2025  
**Versione**: Server-Side Persistence v1.0  
**Architect Review**: ✅ Pass
