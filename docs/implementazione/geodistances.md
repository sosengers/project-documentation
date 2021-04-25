Torna a [Implementazione](../implementazione.md).

## Panoramica

```mermaid
graph LR
ACMESky -->|POST /distance| GD[Geographical Distances API]

GD[Geographical Distances API] -->|Geocoding| OpenCage
```

*Geographical Distances* è il servizio che permette ad *ACMESky* di verificare la distanza fra due luoghi, in modo tale da sapere se dover fornire all'utente un ulteriore servizio di trasporto da e verso l'aeroporto oppure no.

È stato implementato in maniera molto semplice ma, al tempo stesso, esaustiva e il più possibile corretta. Infatti, il servizio funziona nel seguente modo:

- il client (*ACMESky* in questo caso) contatta il servizio mediante le API fornite, in particolare attraverso [POST /distance](../serviziweb/geodistances.md#calculateDistance), specificando nel corpo della richiesta gli indirizzi dei due luoghi geografici di cui calcolare la distanza;
- non c'è un formato particolare richiesto per gli indirizzi da dare in input alle API ma, più precisi e completi sono, migliore è il risultato;
- gli indirizzi dei luoghi vengono trasformati in coordinate GPS tramite un processo di **geocoding**, ossia di conversione da indirizzo testuale a posizione GPS (longitudine, latitudine); per fare ciò ci siamo appoggiati a un servizio esterno disponibile gratuitamente (come *free trial*, abbondantemente sufficiente per gli scopi di questo progetto didattico) chiamato **OpenCage**;
- il geocoding fornito da OpenCage cerca di restituire sempre qualcosa, cercando di trovare una coppia (longitudine, latitudine) anche su sottostringhe dell'indirizzo fornito; per assicurare che non avvengano errori, nell'interfaccia utente di *ACMESky* obblighiamo l'utente a inserire almeno la nazione da cui parte corretta (poiché forniamo la lista completa delle nazioni mediante un menu a tendina); 
- una volta ottenute le due coppie (longitudine, latitudine) relative agli indirizzi di partenza, esse vengono utilizzate per calcolare la **distanza di Haversine** (in Km) fra i due punti, che corrisponde alla distanza in linea d'aria di due punti su di una superficie sferica (è un'approssimazione visto che la Terra non è esattamente una sfera ma, per distanze ridotte come nel caso in esame, non è un grave problema).

Torna a [Implementazione](../implementazione.md)