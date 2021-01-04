<style>
    div#choreographies ul li,
    div#projections ul li {
        list-style-type: none;
	}
</style>
# Coreografie, ruoli e proiezioni delle coreografie sui ruoli

## Sigle e nomenclatura

### Sigle per i ruoli
- AS: ACMESky
- CA<sub>*i*</sub>: Compagnie Aerea *i*, con *i* in [1..N]
- CT<sub>*j*</sub>: Compagnie Trasporto (navetta) *j*, con *j* in [1..M]
- PG: ProntoGram
- PP: Provider dei Pagamenti
- DG: servizio per il calcolo delle Distanze Geografiche
- UT: UTente

### Nomenclature per le interazioni fra ruoli
- **reg**: registrazione dell'interesse di un utente per un viaggio;
- **reg_res**: risposta al task **reg**;
- **control**: controllo giornaliero della presenza di offerte da parte della compagnia aerea;
- **control_res**: risposta al task **control**;
- **notify**: notifica della presenza di voli di interesse per l'utente tramite ProntoGram (possono esserci come no, se non ci sono l'utente non viene contattato). Il codice offerta inviato è univoco per offerta: se ci sono più utenti con gli stessi interessi viene inviato lo stesso codice offerta;
- **last_minute**: compagnia aerea notifica ACMESky di un'offerta last minute;
- **ins_code**: utente inserisce il codice ricevuto via ProntoGram sul portale;
- **req_pay**: ACMESky richiede il pagamento al Provider dei Pagamenti;
- **req_pay_res**: il Provider dei Pagamenti comunica ad ACMESky l'esito del pagamento;
- **calc_dist**: ACMESky richiede il calcolo della distanza al servizio esterno per il calcolo delle distanze geografiche;
- **calc_dist_res**: risposta al task **calc_dist**;
- **pren_trs**: in base alla distanza ACMESky può, oppure no, prenotare il trasporto;
- **pren_trs_res**: risposta al task **pren_trs**;
- **message**: ProntoGram riferisce il messaggio all'Utente (essendo un servizio esterno questa è una semplificazione);
- **pay_offer**: il Provider dei Pagamenti, ricevuta la richiesta da ACMESky, inoltra all'utente la richiesta di pagamento, il quale dovrà soddisfarla per procedere;
- **pay_offer_res**: risposta al task **pay_offer** (invio dati carta per il pagamento);
- **buy_flights**: ACMESky compra per conto dell'utente il biglietto per il volo A/R da lui/lei scelto presso la compagnia aerea CA<sub>*i*</sub> che fornisce i due voli che soddisfano il bisogno;
- **buy_flights_res**: CA<sub>*i*</sub> conferma ad ACMESky la disponibilità del volo e inoltra il biglietto;
- **send_tickets**: ACMESky invia all'utente i biglietti;
- **payment_failure**: ACMESky comunica all'utente che c'è stato un problema durante il pagamento;
- **ins_code_failure**: ACMESky comunica all'utente che il codice inserito non è valido.

## Coreografie
Le seguenti coreografie modellano tutti quanti i possibili processi che possono avvenire nel sistema che verrà implementato.  
Le interazioni che non comprendono AS come mittente o destinatario sono molto semplificate, in quanto esterni ad essa e di cui non possiamo conoscere l'implementazione interna.

<div id="choreographies">

**ProcessoRegistrazioneInteresseUtente** ::=  
(**reg**: UT -> AS; **reg_res**: AS -> UT)

**VerificaGiornaliera** ::=  
(
- ( **control**: AS -> CA<sub>*1*</sub> ; **control_res**: CA<sub>*1*</sub> -> AS ) |  
- ... |  
- ( **control**: AS -> CA<sub>*N*</sub> ; **control_res**: CA<sub>*N*</sub> -> AS )
  
) ;  
(
- ( **notify**: AS -> PG ; **message**: PG -> UT ) + 1  

)

**NotificaVoliLastMinute** ::=  
( **last_minute**: CA<sub>*i*</sub> -> AS ) ;  
(  
- ( **notify**: AS -> PG ; **message**: PG -> UT ) + 1  

)

**AcquistoOfferta** ::=  
( **ins_code**: UT -> AS ) ;  
(  
- ( **req_pay**: AS -> PP ; **pay_offer**: PP -> UT ; **pay_offer_res**: UT -> PP ; **req_pay_res**: PP -> AS ) ;  
- (  
	- ( **buy_flights**: AS -> CA<sub>_i_</sub> ; **buy_flights_res**: CA<sub>_i_</sub> -> AS ) ;  
	- (  
		- (  
		    <!-- Calcolo distanza aeroporto/casa -->
			- ( **calc_dist**: AS -> DG ; **calc_dist_res**: DG -> AS ) ;  
			- (  
				<!-- Calcolo distanze per identificare la compagnia di trasporto più vicina -->
				- ( **calc_dist**: AS -> DG ; **calc_dist_res**: DG -> AS )<sup>\*</sup> ;  
				- ( **pren_trs**: AS -> CT<sub>*j*</sub> ; **pren_trs_res**: CT<sub>*j*</sub> -> AS)

			- )  
			- \+ 1

		- )  
		- \+ 1

	- );    
	- **send_tickets**: AS -> UT

- )  
- \+ **payment_failure**: AS -> UT

)  
\+ **ins_code_failure**: AS -> UT

</div>

### Verifica della connectedness delle coreografie
- **ProcessoRegistrazioneInteresseUtente** è connessa in quanto il ricevente in **reg** è uguale al mittente in **reg_res**.
- **VerificaGiornaliera**: la singola comunicazione fra AS e un qualsiasi CA<sub>*i*</sub> è connessa come per la precedente coreografia, in quanto il ricevente in **control** è uguale al mittente in **control_res**. Le interazioni parallele non hanno condizioni per poter verificare la connectedness e quindi non vengono verificate. Per quanto riguarda la composizione sequenziale fra la prima parte della coreografia, in cui avvengono le interazioni (**control** ; **control_res**) fra AS e i vari CA<sub>*i*</sub>, e la seconda parte, in cui avviene l'interazione **notify** fra AS e PG, è nuovamente assicurata la connectedness. Questo poiché ogni interazione all'interno della composizione parallela termina con ricevente AS e il mittente di **notify** è AS. Dopo **notify** nella sequenza c'è **message** che invia il messaggio ad UT. Essendo AS il ricevente di **notify** e il mittente di **message**, la sequenza è connessa. Nel caso in cui non avvenga **notify** fra AS e PG poiché non vi sono voli per l'utente da comunicare via ProntoGram, allora viene eseguito il ramo "1" (ovvero non viene fatto nulla). In questo caso, PG rimane in attesa di **notify** ma è corretto poiché è il suo compito.
- **NotificaVoliLastMinute** è connessa in modo non dissimile a quanto avviene in **VerificaGiornaliera**. AS è ricevente in **last_minute** e mittente in **notify** dopodiché PG è ricevente in **notify** e mittente in **message**. Da notare che **notify** e **message**, avvengono solamente se ci sono voli di interesse per l'utente e quindi nuovamente, in caso non ce ne siano, ProntoGram resterebbe in attesa ma non sarebbe un problema poiché è il suo compito.
- **AcquistoOfferta** è connessa perché:
  - il ricevente di **ins_code** è il mittente di **req_pay** e di **ins_code_failure** (quest'ultimo termina la coreografia);
  - il ricevente di **req_pay** è il mittente di  **pay_offer**
  - il ricevente di **pay_offer** è il mittente di **pay_offer_res**;
  - il ricevente di **pay_offer_res** è il mittente di **req_pay_res**;
  - il ricevente di **req_pay_res** è il mittente di **buy_flights** e di **payment_failure** (quest'ultimo termina la coreografia);
  - il ricevente di **buy_flights** è il mittente di **buy_flights_res**;
  - il ricevente di **buy_flights_res** è il mittente di **calc_dist** e di **send_tickets** (quest'ultimo termina la coreografia);
  - il ricevente di **calc_dist** è il mittente di **calc_dist_res**;
  - il ricevente di **calc_dist_res** è il mittente di **calc_dist** e di **send_tickets** (quest'ultimo termina la coreografia);
  - il ricevente di **calc_dist** è il mittente di **calc_dist_res** (prima parte della sequenza);
  - il ricevente di **calc_dist_res** è il mittente di **calc_dist** (seconda parte della sequenza che quindi è connessa);
  - il ricevente di **calc_dist_res** (l'ultimo della sequenza lunga almeno uno per ipotesi) è il mittente di **pren_trs**;
  - il ricevente di **pren_trs** è il mittente di **pren_trs_res**;
  - il ricevente di **pren_trs_res** è il mittente di **send_tickets** (quest'ultimo termina la coreografia).

## Proiezioni
Seguono le proiezioni delle coreografie, divise per ruolo.

<div id="projections">

<h3>AS (ACMESky)</h3>

proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, AS) =<br />
( <strong>reg</strong>@UT ; <span style="text-decoration: overline"><strong>reg_res</strong></span>@UT )
<br />
proj(</strong><strong>VerificaGiornaliera</strong>, AS) =<br /> 
(
<ul>
<li>(<span style="text-decoration: overline"><strong>control</strong></span>@CA<sub><em>1</em></sub> ; <strong>control_res</strong>@CA<sub><em>1</em></sub> ) |</li>
<li>... |</li>
<li>(<span style="text-decoration: overline"><strong>control</strong></span>@CA<sub><em>N</em></sub> ; <strong>control_res</strong>@CA<sub><em>N</em></sub> )</li>
</ul>
) ;  <br />
(
<ul>
<li>( <span style="text-decoration: overline"><strong>notify</strong></span>@PG ; 1 ) + 1</li>
</ul>
)
<br />
proj(<strong>NotificaVoliLastMinute</strong>, AS) =<br />
( <strong>last_minute</strong>@CA<sub><em>i</em></sub> ) ;<br />
(
- ( <span style="text-decoration: overline"><strong>notify</strong></span>@PG ; 1 ) + 1  

)

proj(<strong>AcquistoOfferta</strong>, AS) =<br />
( <strong>ins_code</strong>@UT ) ;  
(<br />  
- ( <span style="text-decoration: overline"><strong>req_pay</strong></span>@PP ; 1 ; 1 ; <strong>req_pay_res</strong>@AS ) ;  
- (<br />  
	- ( <span style="text-decoration: overline"><strong>buy_flights</strong></span>@CA<sub><em>i</em></sub> ; <strong>buy_flights_res</strong>@CA<sub><em>i</em></sub> ) ;  
	- (
		- (
			- ( <span style="text-decoration: overline"><strong>calc_dist</strong></span>@DG ; <strong>calc_dist_res</strong>@DG ) ;  
			- (<br />
				- ( <span style="text-decoration: overline"><strong>calc_dist</strong></span>@DG ; <strong>calc_dist_res</strong>@DG )<sup>\*</sup>
				- ( <span style="text-decoration: overline"><strong>pren_trs</strong></span>@CT<sub><em>j</em></sub> ; <strong>pren_trs_res</strong>@CT<sub><em>j</em></sub> )

			- )
			- \+ 1

		- )
		- \+ 1

	- );  
	- <span style="text-decoration: overline">**send_tickets</strong></span>@UT

- )
- \+ <span style="text-decoration: overline"><strong>payment_failure</strong></span>@UT

)  
\+ <span style="text-decoration: overline"><strong>ins_code_failure</strong></span>@UT

### UT (UTente)

proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, UT) =<br />
( <span style="text-decoration: overline"><strong>reg</strong></span>@AS ; <strong>reg_res</strong>@AS )

proj(<strong>VerificaGiornaliera</strong>, UT) =<br />
(<br />
- ( 1 ; 1 ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(<br />
- ( 1 ; <strong>message</strong>@PG ) + 1  

)  
= <strong>message</strong>@PG

proj(<strong>NotificaVoliLastMinute</strong>, UT) =<br />
( 1 ) ;  
(<br />
- ( 1 ; <strong>message</strong>@PG ) + 1  

)  
= <strong>message</strong>@PG

proj(<strong>AcquistoOfferta</strong>, UT) =<br />
( <span style="text-decoration: overline"><strong>ins_code</strong></span>@AS ) ;  
(<br />
- ( 1 ; <strong>pay_offer</strong>@PP ; <span style="text-decoration: overline"><strong>pay_offer_res</strong></span>@PP ; 1 ) ;  
- (<br />  
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( 1 ; 1 )
			- (<br />
				- ( 1 ; 1 )<sup>\*</sup>
				- ( 1 ; 1 )

			- )
			- \+ 1

		- )
		- \+ 1

	- ) ;
	- **send_tickets</strong>@AS

- )
- \+ <strong>payment_failure</strong>@AS

)
\+ <strong>ins_code_failure</strong>@AS

### DG (Distanze Geografiche)

proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, DG) =<br />
( 1 ; 1 ) = 1

proj(<strong>VerificaGiornaliera</strong>, DG) =<br />
(<br />
- (1 ; 1) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(<br />
- ( 1 ; 1 ) + 1  

)  
= 1

proj(<strong>NotificaVoliLastMinute</strong>, DG) =<br />
( 1 ) ;  
(<br />
- ( 1 ; 1 ) + 1  

)  
= 1

proj(<strong>AcquistoOfferta</strong>, DG) =<br />
( 1 ) ;  
(<br />  
- ( 1 ; 1 ; 1 ; 1 ) ;  
- (<br />  
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( <strong>calc_dist</strong>@AS ; <span style="text-decoration: overline"><strong>calc_dist_res</strong></span>@AS ) ;  
			- (<br />
				- ( <strong>calc_dist</strong></span>@AS ; <span style="text-decoration: overline"><strong>calc_dist_res</strong></span>@AS )<sup>\*</sup>
				- ( 1 ; 1 )

			- )
			- \+ 1

		- )
		- \+ 1

	- );  
	- 1

- )
- \+ 1

)  
\+ 1

### PP (Provider dei Pagamenti)

proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, PP) =<br />
( 1 ; 1 ) = 1

proj(<strong>VerificaGiornaliera</strong>, PP) =<br />
(<br />
- ( 1 ; 1 ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(<br />
- ( 1 ; 1 ) + 1  

) = 1

proj(<strong>NotificaVoliLastMinute</strong>, PP) =<br />
( 1 ) ;  
(<br />
- ( 1 ; 1 ) + 1  

) = 1

proj(<strong>AcquistoOfferta</strong>, PP) =<br />
( 1 ) ;  
(<br />  
- ( <strong>req_pay</strong>@AS ; <span style="text-decoration: overline"><strong>pay_offer</strong></span>@UT ; <strong>pay_offer_res</strong>@UT ; <span style="text-decoration: overline"><strong>req_pay_res</strong>@AS</span> ) ;  
- (<br />  
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( 1 ; 1 ) ;  
			- (<br />
				- ( 1 ; 1 )<sup>\*</sup>
				- ( 1 ; 1 )

			- )
			- \+ 1

		- )
		- \+ 1

	- );  
	- 1

- )
- \+ 1

)  
\+ 1

### PG (ProntoGram)

proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, PG) =<br />
( 1 ; 1 ) = 1

proj(<strong>VerificaGiornaliera</strong>, PG) =<br />
(<br />
- ( 1 ; 1 ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(<br />
- ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT ) + 1  

) = ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT )  

proj(<strong>NotificaVoliLastMinute</strong>, PG) =<br />
( 1 ) ;  
(<br />
- ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>notify</strong></span>@UT ) + 1  
 
) = ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>notify</strong></span>@UT )

proj(<strong>AcquistoOfferta</strong>, PG) =<br />
( 1 ) ;  
(<br />  
- ( 1 ; 1 ; 1 ; 1 ) ;  
- (<br />  
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( 1 ; 1 ) ;  
			- (<br />
				- ( 1 ; 1 )<sup>\*</sup>
				- ( 1 ; 1 )

			- )
			- \+ 1

		- )
		- \+ 1

	- );  
	- 1

- )
- \+ 1

)  
\+ 1

### CT<sub><em>j</em></sub> (Compagnia Trasporti)

proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, CT<sub><em>j</em></sub>) =<br />
( 1 ; 1 ) = 1

proj(<strong>VerificaGiornaliera</strong>, CT<sub><em>j</em></sub>) =<br />
(<br />
- ( 1 ; 1 ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(<br />
- ( 1 ; 1 ) + 1  

) = 1

proj(<strong>NotificaVoliLastMinute</strong>, CT<sub><em>j</em></sub>) =<br />
( 1 ) ;  
(<br />
- ( 1 ; 1 ) + 1  

) = 1

proj(<strong>AcquistoOfferta</strong>, CT<sub><em>j</em></sub>) =<br />
( 1 ) ;  
(<br />  
- ( 1 ; 1 ; 1 ; 1 ) ;  
- (<br />  
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( 1 ; 1 ) ;  
			- (<br />
				- ( 1 ; 1 )<sup>\*</sup>
				- ( <strong>pren_trs</strong></span>@AS; <span style="text-decoration: overline"><strong>pren_trs_res</strong></span>@AS )

			- )
			- \+ 1

		- )
		- \+ 1

	- );  
	- 1

- )
- \+ 1

)  
\+ 1

### CA<sub><em>i</em></sub> (Compagnia Aerea)

proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, CA<sub><em>i</em></sub>) =<br />
( 1 ; 1 ) = 1

proj(<strong>VerificaGiornaliera</strong>, CA<sub><em>i</em></sub>) =<br />
(<br />
- (1 ; 1) |  
- ... |  
- ( <strong>control</strong>@AS ; <span style="text-decoration: overline"><strong>control_res</strong></span>@AS ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(<br />
- ( 1 ; 1 ) + 1  

) = ( <strong>control</strong>@AS ; <span style="text-decoration: overline"><strong>control_res</strong></span>@AS )

proj(<strong>NotificaVoliLastMinute</strong>, CA<sub><em>i</em></sub>) =<br />
( <span style="text-decoration: overline"><strong>last_minute</strong></span>@AS ) ;  
(<br />
- ( 1 ; 1 ) + 1  

) = <span style="text-decoration: overline"><strong>last_minute</strong></span>@AS

proj(<strong>AcquistoOfferta</strong>, CA<sub><em>i</em></sub>) =<br />
( 1 ) ;  
(<br />  
- ( 1 ; 1 ; 1 ; 1 ) ;  
- (<br />  
	- ( <strong>buy_flights</strong>@AS ; <span style="text-decoration: overline"><strong>buy_flights_res</strong></span>@CA ) ;  
	- (
		- (
			- ( 1 ; 1 ) ;  
			- (<br />
				- ( 1 ; 1 )<sup>\*</sup>
				- ( 1 ; 1 )

			- )
			- \+ 1

		- )
		- \+ 1

	- );  
	- 1

- )
- \+ 1

)  
\+ 1

</div>
