# Scout Triangolo

Web app per raccogliere le osservazioni degli scout durante i tornei (scheda Spielerprofil SFV).
Dati in Firebase (Authentication + Firestore), sincronizzati in tempo reale. Codice statico, pubblicabile con GitHub Pages.

## File

| File | A cosa serve |
|---|---|
| `index.html` | L'app intera (interfaccia + logica) |
| `firebase-config.js` | Configurazione del progetto Firebase: **da compilare** |
| `firestore.rules` | Regole di sicurezza: chi non ha un accesso attivo non vede nulla |
| `firebase.json` | Serve solo per test locali con l'emulatore |

## Messa in funzione (una volta sola, circa 20 minuti)

### 1. Progetto Firebase
1. Vai su https://console.firebase.google.com → **Aggiungi progetto** (es. `scout-triangolo`). Google Analytics non serve.
2. Resta sul piano gratuito **Spark**: non serve la carta di credito.

### 2. Accesso con email e password
**Build → Authentication → Inizia → Metodo di accesso → Email/password → Abilita → Salva.**

### 3. Database
**Build → Firestore Database → Crea database**
- Località: `europe-west6 (Zurigo)` (non si può cambiare dopo)
- Modalità: **produzione**

### 4. Regole di sicurezza
**Firestore Database → Regole**: cancella tutto, incolla il contenuto di `firestore.rules` → **Pubblica**.

### 5. Collegare l'app
**⚙️ Impostazioni progetto → Le tue app → icona Web `</>`** → nome `scout-triangolo` (niente Hosting) → **Registra app**.
Copia i valori di `firebaseConfig` dentro `firebase-config.js` (sostituisci tutti gli `INCOLLA_QUI`).
Non sono dati segreti: possono stare su GitHub.

### 6. Il primo head coach (a mano, solo questa volta)
1. **Authentication → Utenti → Aggiungi utente**: email e password dell'head coach. Copia l'**UID** che compare nella lista.
2. **Firestore Database → Dati → + Avvia raccolta**
   - ID raccolta: `users`
   - ID documento: **incolla l'UID** (non usare "ID automatico")
   - Campi:

| Campo | Tipo | Valore |
|---|---|---|
| `nome` | string | Nome dell'head coach |
| `email` | string | la stessa email |
| `ruolo` | string | `coach` |
| `lingua` | string | `it` (oppure `de` / `fr`) |
| `attivo` | boolean | `true` |

Da qui in poi gli altri accessi (scout o altri head coach) si creano **dall'app**, nella scheda **Scout**.

### 7. Pubblicare su GitHub Pages
1. Crea un repository su GitHub (es. `scout-triangolo`) e carica i file.
2. **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `/ (root)` → Save.**
3. Dopo 1–2 minuti l'app è su `https://<tuo-utente>.github.io/scout-triangolo/`.

### 8. Autorizzare l'indirizzo dell'app
**Authentication → Impostazioni → Domini autorizzati → Aggiungi dominio** → `<tuo-utente>.github.io`.

## Come funziona l'accesso
- Si entra solo con **email e password**. Un account deve avere un profilo in `users` con `attivo = true`.
- Un account senza profilo, o disattivato, viene buttato fuori subito e non legge niente: lo bloccano le regole del database, non solo l'app.
- Se l'head coach disattiva uno scout mentre questo è collegato, lo scout esce subito.
- **L'head coach può:** creare accessi, disattivarli e riattivarli, e mandare l'email per reimpostare la password.
- **Lo scout può:** compilare schede a proprio nome, vedere solo le proprie, modificarle finché sono "bozza" e inviarle (dopo l'invio non può più cambiarle), e cambiare la propria lingua.
- **L'head coach vede tutte le schede** e può modificarle, inviarle, riaprirle o eliminarle in qualsiasi momento.
- **Dopo ogni modifica a `firestore.rules`** va ripubblicato il testo in Firebase → Firestore → Regole → Pubblica.

## Limiti di questa prima versione (piano gratuito)
- **Cancellare un account:** dall'app un account si può solo disattivare. Per cancellarlo del tutto: Firebase → Authentication → Utenti → elimina.
- **Password:** l'head coach non vede e non cambia le password. Può solo far partire l'email di reimpostazione, quindi lo scout deve avere un'email vera.
- **Creazione non completata:** se la creazione di un accesso si interrompe a metà (rete), l'email resta "già usata". In quel caso elimina l'utente in Authentication e riprova.
- **Voci in italiano:** sono provvisorie, in attesa della tabella ufficiale.
- **"Genera testo":** è una bozza. Lo stile si definisce con gli esempi dell'head coach.

## Test locale (facoltativo, per sviluppatori)
```
firebase emulators:start --only auth,firestore --project demo-triangolo
python3 -m http.server 8000      # poi apri http://localhost:8000/?emulator
```
