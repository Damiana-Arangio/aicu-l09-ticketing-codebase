# Codebase Reading Notes - L09

## Comportamento Osservato

- Operatore corrente: esposto dall’endpoint `GET /api/me` in `server/index.js`; il server individua l’operatore corrente importando `currentOperatorId` e `operators` da `server/data/operators.js`. Il frontend chiama `fetchCurrentOperator` da `src/api.js` dentro `App.jsx`, salva l’intero oggetto operatore nello stato `operator` e nella dashboard mostra solo la proprietà `name`, quindi il nome dell’operatore.

- Lista ticket: esposta dall’endpoint `GET /api/tickets` in `server/index.js`; il server importa i ticket da `server/data/tickets.js` e restituisce solo quelli con `status === "open"`. Se nella query viene passato `empty=true`, restituisce una lista vuota. Il frontend chiama `fetchOpenTickets` da `src/api.js` dentro `App.jsx`, salva l’array di ticket nello stato `tickets` e lo passa a `TicketList`, che mostra il numero di ticket aperti e renderizza una `TicketCard` per ogni ticket. La card mostra `id`, `priority`, `title`, `description`, `customerName`, `updatedAt`, `area` e `sourceChannel`; il campo derivato è presente nella UI, ma risulta ancora indicato come “non ancora calcolato”.

- Create ticket: il frontend chiama `fetchTicketOptions` da `src/api.js` dentro `App.jsx`, salva le opzioni nello stato `options` e le passa a `CreateTicketPanel`. Il form raccoglie `title`, `description`, `requesterEmail` e `priority`, mentre `area` e `sourceChannel` non sono ancora presenti. Al submit, `CreateTicketPanel` chiama `createTicket(form)` da `src/api.js`, che invia il payload JSON all’endpoint `POST /api/tickets`. In `server/index.js`, però, questo endpoint restituisce `501 NOT_IMPLEMENTED`, quindi la creazione del ticket non è ancora implementata.


## File Chiave

| File | Perche' e' importante | Evidenza |
| --- | --- | --- |
| `server/index.js` | È importante perché contiene le API usate dalla dashboard e permette di vedere cosa funziona già e cosa è ancora incompleto. | Contiene `GET /api/me`, `GET /api/tickets`, `GET /api/ticket-options` e `POST /api/tickets`. Il `POST /api/tickets` restituisce `501 NOT_IMPLEMENTED`. |
| `src/components/CreateTicketPanel.jsx` | È importante perché mostra il form di creazione ticket e permette di capire quali campi possono essere compilati oggi nella UI. | Il form gestisce `title`, `description`, `requesterEmail` e `priority`, ma non contiene ancora campi per `area` e `sourceChannel`. Al submit chiama `createTicket(form)`. |
| `prisma/schema.prisma` | È importante perché mostra il modello dati previsto per `Operator` e `Ticket`, anche se Prisma non è ancora collegato all’app. | Nel model `Ticket` sono presenti `area`, `sourceChannel`, `responseDueAt` e `urgencyLabel`. Il commento indica che `responseDueAt` è un campo derivato dal server: `priority + area -> scadenza di risposta`. |

## Gap Osservati

| Area | Gap | Evidenza |
| --- | --- | --- |
| Backend / API | La creazione del ticket non è ancora implementata. | In `server/index.js`, l’endpoint `POST /api/tickets` restituisce `501 NOT_IMPLEMENTED`, quindi il ticket non viene ancora creato realmente. |
| UI / Form create | Il form di creazione non usa ancora tutte le opzioni disponibili per il ticket. | `GET /api/ticket-options` restituisce `priorities`, `areas` e `sourceChannels`, ma in `CreateTicketPanel.jsx` il form usa solo `options.priorities` e raccoglie solo `title`, `description`, `requesterEmail` e `priority`. Mancano ancora `area` e `sourceChannel`. |
| Campo derivato | La scadenza di risposta e l’etichetta di urgenza non vengono ancora gestite. | `TicketCard.jsx` mostra il campo derivato come “non ancora calcolato”; in `prisma/schema.prisma` sono presenti `responseDueAt` e `urgencyLabel`. Il commento indica `responseDueAt` come campo derivato dal server a partire da `priority + area`, mentre `urgencyLabel` è previsto nel modello ma non ancora valorizzato. |


## Domanda Per L10

- In L10 dovremo limitarci ad aggiungere `area` e `sourceChannel` al form usando le opzioni esposte da `GET /api/ticket-options`, oppure dovremo anche preparare la UI per la futura creazione del ticket e per la gestione del campo derivato `responseDueAt`?
