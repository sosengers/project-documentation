---
hide:
  - navigation
---
# ACMESky - Documentazione progetto

Il seguente sito web descrive i processi che hanno portato alla realizzazione del progetto didattico del corso Ingegneria del Software Orientata ai Servizi della LM Informatica dell'Università di Bologna svolto da Marco Ferrati e Tommaso Azzalin nell' A.A. 2020/21.

## Descrizione del progetto
Realizzare una Service Oriented Architecture (SOA) che rappresenti la realtà di una compagnia chiamata ACMESky, la quale fornisce ai propri utenti la possibilità di venire notificati e acquistare offerte di voli a/r in determinate date e al di sotto di una certa soglia di prezzo, da loro scelte in precedenza.

ACMESky si relaziona con diversi servizi esterni all'organizzazione:

- Le compagnie aeree per il controllo della presenza di offerte e l'acquisto dei voli;
- Le compagnie di noleggio con autista da noi rinominate "Compagnie di trasporto" per la prenotazione del trasporto gratuito degli utenti da casa all'aeroporto;
- Il servizio per il calcolo delle distanze geografiche;
- Il sistema bancario da noi rinominato "Provider di pagamenti" che si occupa di gestire i pagamenti per l'acquisto dei voli da parte degli utenti;
- Il servizio ProntoGram, con il quale contattare gli utenti per comunicar loro i codici delle offerte.

## Struttura del sito
Il seguente sito è strutturato come segue:
	
- [Coreografie](coreografie.md): descrizione delle coreografie da noi progettate, la verifica della loro connectedness e le loro proiezioni sui diversi ruoli;
- [BPMN](bpmn.md): descrizione delle coreografie sotto forma di diagrammi BPMN;
- [UML](uml.md): descrizione della SOA tramite diagrammi UML con profilo TinySOA;
- [Implementazione](implementazione.md): descrizione dei processi e delle decisioni con cui si è passati dalla progettazione all'implementazione.
