---
hide:
  - navigation
---
# ACMESky - Documentazione progetto

Il seguente report descrive i processi che hanno portato alla realizzazione del progetto didattico del corso **Ingegneria del Software Orientata ai Servizi** della LM Informatica dell'Università di Bologna, svolto da Marco Ferrati e Tommaso Azzalin nell'A.A. 2020/2021.

## Descrizione del progetto
Realizzare una **Service Oriented Architecture** (**SOA**) che rappresenti la realtà di una compagnia chiamata *ACMESky*.  
*ACMESky* fornisce ai propri utenti la possibilità di venire notificati e successivamente acquistare offerte di voli A/R in determinate date e al di sotto di una certa soglia di prezzo, da loro scelte in precedenza.

*ACMESky* si relaziona con diversi servizi esterni all'organizzazione:

- le *compagnie aeree* (o *Flight Company*) per il controllo della presenza di offerte e l'acquisto dei voli;
- le *compagnie di noleggio con autista*, da noi rinominate in *compagnie di trasporto* (o *Travel Company*), per la prenotazione del trasporto gratuito degli utenti da casa all'aeroporto e viceversa;
- il servizio per il calcolo delle *distanze geografiche* (o *Geographical Distances*);
- il sistema bancario, da noi rinominato in *provider di pagamenti* (o *Payment Provider*), che si occupa di gestire i pagamenti per l'acquisto dei voli da parte degli utenti;
- il servizio *ProntoGram*, con il quale contattare gli utenti per comunicar loro i codici delle offerte.

### Assunzioni e semplificazioni

Essendo un progetto a scopo didattico sono state adottate alcune assunzioni e semplificazioni sul funzionamento dei vari servizi, che vengono elencati di seguito:

- gli aspetti relativi alla sicurezza delle comunicazioni e dei dati sono stati ignorati mentre, ad esempio, sarebbe stato corretto utilizzare il protocollo HTTPS per le comunicazioni, gestire l'autenticazione degli utenti su *ProntoGram*, separare i dati in diverse tabelle e cifrare quelli personali;
- i voli offerti dalle *compagnie aeree* sono tutti diretti e quando vengono assegnati ad un offerta *ACMESky* chiede di riservare un posto alle *compagnie aeree* in modo da evitare, al momento dell'acquisto, un errore dovuto alla mancanza di disponibilità di posti a sedere;
- un'offerta di viaggio è composta di solo due voli di un'unica *compagnia aerea*, quelli con prezzo inferiore in tutto il range di date indicato dall'utente, questo non implica che sia la scelta ottima per i desideri dell'utente;
- l'acquisto di un trasporto tramite una *compagnia di noleggio con autista* viene implementato in maniera molto basilare ed è sempre in grado di soddisfare le richieste di prenotazione da parte di *ACMESky*.

## Struttura del sito
Il seguente sito è strutturato come segue:
	
- [Coreografie](coreografie.md): descrizione delle coreografie da noi progettate, la verifica della loro connectedness e le loro proiezioni sui diversi ruoli;
- [Coreografie BPMN](coreografiebpmn.md): descrizione delle coreografie della precedente sezione mediante coreografie BPMN;
- [BPMN](bpmn.md): descrizione delle coreografie sotto forma di diagrammi BPMN;
- [UML](uml.md): descrizione della SOA tramite diagrammi UML con profilo TinySOA;
- [Servizi web](serviziweb.md): presentazione delle interfacce tramite cui è possibile contattare i servizi web dei diversi partecipanti del sistema realizzato;
- [Implementazione](implementazione.md): descrizione dei processi e delle decisioni con cui si è passati dalla progettazione all'implementazione;
- [Istruzioni per l'esecuzione](executeinstruction.md): descrizione esaustiva su come passare dal codice sorgente del progetto al sistema in esecuzione, passo dopo passo.
