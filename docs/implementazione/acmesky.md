Torna a [Implementazione](../implementazione.md).

## Panoramica

```mermaid
graph TD
CW <--> DBs
CW <--> OS[Other Services]
CW <--> C
CW <--> R
BE --> C
FE <--> BE
FE <--> MW
Utente <--> FE
MW --> R

subgraph ACMESky
DBs[(Databases)]
CW[Camunda workers]
C[Camunda]
BE[Backend]
R[RabbitMQ]
FE[Frontend]
MW[Middleware]
end
```

ACMESky è quell'insieme di componenti che permettono ad un utente di inserire un proprio interesse all'interno del sistema e di acquistare i voli per un viaggio.

### Interazioni Utente e ACMESky
```mermaid
graph TD
BE -->|Correlate messages| C
FE <-->|POST /buy/offers| BE
FE <-->|POST /register_interest| BE
FE <-->|WebSocket| MW
Utente <--> FE
MW -->|Subscribe| R

subgraph ACMESky
C[Camunda]
BE[Backend]
R[RabbitMQ]
FE[Frontend]
MW[Middleware]
end
```

L'utente interagisce con l'interfaccia Web messa a disposizione dal Frontend. Il Frontend mette a disposizione due funzionalità, l'aggiunta di un nuovo interesse e l'acquisto di un viaggio. Queste due operazioni sono permesse da due chiamate HTTP al Backend di ACMESky: `POST /registe/interest` e `POST /buy/offers`.
![!Homepage ACMESky](../assets/implementazione/homepage_acmesky.png)

#### Aggiunta di un nuovo interesse

![!Aggiunta di un nuovo interesse](../assets/implementazione/registrazione_interesse.png)

Un utente per aggiungere il proprio interesse inserisce i dati richiesti e preme il pulsante "Conferma". Il backend a sua volta manda un messaggio a Camunda contenente i dati inseriti dall'utente avviando così un nuovo processo nell'engine i cui task verranno svolti da alcuni worker. Un task aggiunge l'interesse all'interno di mongoDB, questo database verrà riconttato quando verranno controllati gli interessi non ancora soddisfatti alla ricerca di offerte che matchano i requisiti salvati.

#### Acquisto di un'offerta

![!Inserimento dati acquisto offerta](../assets/implementazione/acmesky_inserimento_dati_offerta.png)

Quando un utente riceve il codice offerta tramite ProntoGram si può recare su questa pagine e, riempiendo il form con i dati richiesti avviare il processo di acquisto di un'offerta. Il Frontend contatta il Backend passandogli i dati inseriti dall'utente e, a sua volta, invia un messaggio a Camunda che avvia un nuovo processo. Il backend risponde con il codice che verrà usato per la comunicazione tra Frontend e Middleware tramite WebSocket. Quando un worker deve comunicare con il frontend pubblica un messaggio sulla coda RabbitMQ utilizzando lo stesso codice comunicato al Frontend e il Middleware, essendosi sottoscritto alla stessa coda utilizza il WebSocket per comunicare il messaggio al giusto utente.

In questo modo è possibile comunicare errori:
![!Comunicazione errori al Frontend](../assets/implementazione/acmesky_error.png)

Richiedere il pagamento da parte dell'utente:
![!Richiesta pagamento](../assets/implementazione/acmesky_pagamento.png)

E mostrare l'offerta acquistata:
![!Biglietti acquistati](../assets/implementazione/acmesky_biglietti.png)

### Interazioni ACMESky e servizi esterni

Per poter compiere i diversi task, i worker contattano dei servizi che sono esterni ad ACMESky

```mermaid
graph LR 
CW <--> FC[Flight Company]
CW <--> TC[Travel Company]
CW <--> GD[Geographical distances]
CW <--> PP[Payment Provider]
CW <--> PG[ProntoGram]
CW <--> R

subgraph ACMESky
CW[Camunda workers]
R[RabbitMQ]
end

subgraph OS[Servizi esterni]
FC[Flight Company]
TC[Travel Company]
GD[Geographical distances]
PP[Payment Provider]
PG[ProntoGram]
end
```

Torna a [Implementazione](../implementazione.md).
