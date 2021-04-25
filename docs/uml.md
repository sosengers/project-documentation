---
hide:
  - navigation
---

In questa sezione viene rappresentata, sotto forma di diagrammi UML con profilo **TinySOA**, la modellazione della SOA di cui fa parte l'organizzazione *ACMESky*. I seguenti diagrammi hanno lo scopo di evidenziare, per ogni servizio, quali sono le *capability* accessibili tramite il sistema e le interfacce che le espongono, esternamente e/o internamente, da ogni organizzazione facente parte della SOA.  
In particolare, si distinguono tre tipi di servizi:

- **Task** (o **Process**): espone *capability* realizzate attraverso processi interni all'organizzazione, eventualmente svolti da umani. Sono strettamente legati al dominio del problema;
- **Entity**: corrisponde a una singola attività, solitamente automatica (per esempio, il salvataggio di un record in una base di dati);
- **Utility**: come i Task, ma non sono prettamente legati al dominio del problema.

## Registrazione interesse di un utente
![!Diagramma UML che descrive come vengono implementati i task del processo di registrazione di un interesse di un utente](assets/uml/RegistrazioneInteresseUtente.png){: loading=lazy}

Nel diagramma sovrastante sono evidenziate le capability emerse dall'analisi del diagramma BPMN "Registrazione interesse utente" e le interfacce che le espongono.

Le capability emerse, per il ruolo di *ACMESky* sono: `Interest` e `InterestRegistration`. Queste due capability sono esposte mediante due interfacce e la capability `InterestRegistration` dipende dall'interfaccia che espone la capability `Interest`. Queste due capability permettono al sistema di recepire l'interesse da parte di un utente per un viaggio e salvarlo in maniera da poterlo riutilizzare per successivi controlli. 

Inoltre, dal diagramma BPMN è emersa anche un'altra capability, questa volta relativa all'utente: `OperationResult`. Questa capability permette all'utente di recepire il successo o il fallimento dell'inserimento del suo interesse all'interno del sistema. L'interfaccia che espone quest'ultima capability è una dipendenza della capability `InterestRegistration`.

## Verifica giornaliera delle offerte
![!Diagramma UML che descrive come vengono implementati i task del processo di verifica giornaliera delle offerte delle compagnie aree e notifica degli utenti](assets/uml/VerificaGiornaliera.png){: loading=lazy}

Nel diagramma sovrastante sono evidenziate le capability emerse dall'analisi del diagramma BPMN "Verifica giornaliere delle offerte" e le interfacce che le espongono.

Le capability emerse, per il ruolo di *ACMESky* sono: `DailyOffersCheck` e `Offer`. Queste due capability sono esposte mediante tre interfacce e la capability `DailyOffersCheck` dipende dalle interfacce `OfferSaving` e `OfferFinding` che espongono la capability `Offer`. Queste due capability permettono al sistema di memorizzare le offerte delle compagnie aeree e successivamente verificare se esse sono coerenti con gli interessi dichiarati dai suoi utenti.
`DailyOffersCheck` dipende inoltre da un'altra capability, `FlightOffersRetrieval` di *Flight Company* (una compagnia aerea), per ricevere le offerte di voli delle compagnie.
La capability `Offer`, invece, dipende dalla capability di invio messaggi di *ProntoGram*, `Message`, esposta attraverso un'interfaccia.

Infine, per contattare l'utente, la capability `Message` di *ProntoGram* dipende da un ulteriore capability `MessagePublishing` con la quale notificare l'utente della presenza di nuovi messaggi a lui indirizzati.

## Ricezione offerte last minute
![!Diagramma UML che descrive come vengono implementati i task del processo di ricezione di offerte dalle compagnie aree e notifica degli utenti](assets/uml/NotificaVoliLastMinute.png){: loading=lazy}

Nel diagramma sovrastante sono evidenziate le capability emerse dall'analisi del diagramma BPMN "Acquisto offerta da un utente" e le interfacce che le espongono.

Le capability emerse, per il ruolo di *ACMESky* sono: `LastMinuteOffersPublishing` e `Offer`. Queste due capability sono esposte mediante tre interfacce e la capability `LastMinuteOffersPublishing` dipende dalle interfacce `OfferSaving` e `OfferFinding` che espongono la capability `Offer`. Queste due capability permettono al sistema di memorizzare le offerte last-minute ricevute direttamente dalle compagnie aeree e successivamente verificare se esse sono coerenti con gli interessi dichiarati dai suoi utenti.

Le capability `Offer`, `Message`, `MessagePublishing` e relative interfacce sono le stesse che sono illustrate nella precedente sezione.

## Acquisto offerta da un utente
![!Diagramma UML che descrive come vengono implementati i task del processo di acquisto di un'offerta](assets/uml/AcquistoOfferta.png){: loading=lazy}

Nel diagramma sovrastante sono evidenziate le capability emerse dall'analisi del diagramma BPMN "Acquisto offerta da un utente" e le interfacce che le espongono. 

Le capability emerse per il ruolo di *ACMESky* sono: `OfferCodeInsertion`, `PaymentHandler`, `Payment`, `Offer` e `Distance`; ognuna di queste capability è esposta da una specifica interfaccia. Queste capability permettono al sistema di: ricevere la richiesta di acquisto di un'offerta da parte di un utente, ricevere l'esito di un'operazione di pagamento, verificare l'esito di un pagamento, verificare il codice dell'offerta inserito da un utente e controllare la distanza geografica tra due coordinate. La capability `OfferCodeInsertion` dipende dalle interfacce che espongono la capability `Offer` per poter verificare la validità del codice inserito e `Distance` per controllare la distanza tra la casa dell'utente e l'aeroporto. La capability `PaymentHandler` dipende dall'interfaccia che espone la capability `Payment` per verificare l'esito del pagamento. 

Per il ruolo di *PaymentProvider* abbiamo individuato la capability `PaymentRequest` esposta da due interfacce `PaymentRequest` e `PaymentHandler`. L'interfaccia `PaymentRequest` è una dipendenza della capability `OfferCodeInsertion` per poter creare la richiesta di un pagamento da richiedere poi all'utente. 

Le capability emerse per il ruolo di User sono `PaymentRequest` e `OperationResult`, ognuna esposta da un interfaccia specifica. L'interfaccia di `PaymentRequest` è una dipendenza della capability `PaymentRequest` del ruolo *PaymentProvider* per permettere al provider di pagamenti di richiedere all'utente i dati per effettuare un pagamento; a sua volta la capability `PaymentRequest` è una dipendenza dell'interfaccia `PaymentHandler` per poter interagire con il provider di pagamento. Un ulteriore capability associata al ruolo di User è `OperationResult` la quale permette all'utente di ricevere messaggi di successo o di errore da parte di altri ruoli; infatti l'interfaccia che espone tale capability è una dipendenza della capability `OfferCodeInsertion` e `PaymentHandler` di *ACMESky*.

Il ruolo *TravelCompany* possiede la capability BookTransfer e l'interfaccia che la espone è una dipendenza di `OfferCodeInsertion` per poter prenotare il servizio di traporto in caso l'utente ne abbia diritto.

Il ruolo *GeographicDistance* possiede la capability `DistanceComputation` e, come per la precedente, l'interfaccia che la espone è una dipendenza di `OfferCodeInsertion` per poter calcolare la distanza tra due punti geografici.

Infine, il ruolo *FlightCompany* ha la capability `BuyFlights` la cui interfaccia è dipendenza di `OfferCodeInsertion` per permettere ad *ACMESky* ti acquistare i voli di interesse per l'utente.
