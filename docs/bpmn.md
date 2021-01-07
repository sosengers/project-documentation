---
hide:
  - navigation
---

In questa sezione vengono rappresentati sotto forma di diagrammi BPMN le coreografie discusse nella [sezione precedente](coreografie.md). I seguenti diagrammi sono solo a scopo documentativo di conseguenza il loro livello di dettaglio è finalizzato a questo fine. Infatti abbiamo tralasciato la maggior parte della gestione degli errori che viene discussa nell'[implementazione](implementazione.md).

## Registrazione interesse di un utente
![!BPMN processo registrazione utente](assets/bpmn/RegistrazioneInteresseUtente.png){: loading=lazy}

## Verifica giornaliera delle offerte
![!BPMN processo verifica giornaliera delle offerte](assets/bpmn/VerificaGiornaliera.png){: loading=lazy}

## Ricezione offerte last minute
![!BPMN processo ricezione offerte last minute](assets/bpmn/NotificaVoliLastMinute.png){: loading=lazy}

## Acquisto offerta da un utente
![!Diagramma BPMN che descrive l'acquisto di un'offerta](assets/bpmn/AcquistoOfferta.png){: loading=lazy}

Il diagramma descrive il processo di acquisto di un offerta da parte di un utente. Il processo inizia con la ricezione, attraverso il Portale Web di ACMESky, del codice offerta ricevuto da un utente. Ne viene immediatamente verificata la validità tramite un Service Task e, in caso il codice non sia valido, viene avvisato l'utente e il processo termina. Invece, nel caso sia valido, il processo prosegue facendo richiesta al Provider dei Pagamenti di richiedere il pagamento all'utente, il quale invia i dati per il pagamento al Provider dei Pagamenti che, una volta elaborati, invia l'esito della transazione ad ACMESky. In caso l'esito della transazione sia negativo, ACMESky comunica all'utente che c'è stato un problema con il pagamento e il processo termina; in caso di esito positivo il processo procede nell'esecuzione. ACMESky acquista i biglietti aerei dell'offerta attraverso il servizio della Compagnia Aerea, la quale restituisce ad ACMESky i biglietti. In caso il prezzo totale  dei viaggi superi i 1.000 € e il cliente viva entro 30 Km dall'aeroporto, ACMESky identifica la compagnia di trasporto con autista più vicina all'abitazione del cliente per prenotare il trasferimento da/verso l'aeroporto. Il calcolo delle distanze viene fatto tramite il servizio Distanze Geografiche. Infine ACMESky invia all'utente i biglietti aerei e, in caso sia stato prenotato, i biglietti per il trasferimento da/verso l'aeroporto. Il processo può quindi concludersi. 

