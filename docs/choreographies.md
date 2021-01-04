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

<strong>ProcessoRegistrazioneInteresseUtente</strong> ::=<br>
(<strong>reg</strong>: UT -> AS; <strong>reg_res</strong>: AS -> UT)

<strong>VerificaGiornaliera</strong> ::=<br>
(
<ul>
<li> ( <strong>control</strong>: AS -> CA<sub><em>1</em></sub> ; <strong>control_res</strong>: CA<sub><em>1</em></sub> -> AS ) | </li>
<li> ... |</li>
<li> ( <strong>control</strong>: AS -> CA<sub><em>N</em></sub> ; <strong>control_res</strong>: CA<sub><em>N</em></sub> -> AS ) </li>
</ul>
) ;<br>
(
<ul>
<li> ( <strong>notify</strong>: AS -> PG ; <strong>message</strong>: PG -> UT ) + 1</li>
</ul>
)

<br>
<br>

<strong>NotificaVoliLastMinute</strong> ::=<br>
( <strong>last_minute</strong>: CA<sub><em>i</em></sub> -> AS ) ;  
(  
<ul>
<li>( <strong>notify</strong>: AS -> PG ; <strong>message</strong>: PG -> UT ) + 1</li>  
</ul>
)

<br>
<br>

<strong>AcquistoOfferta</strong> ::=  <br>
( <strong>ins_code</strong>: UT -> AS ) ;<br>  
(  
<ul>
<li> ( <strong>req_pay</strong>: AS -> PP ; <strong>pay_offer</strong>: PP -> UT ; <strong>pay_offer_res</strong>: UT -> PP ; <strong>req_pay_res</strong>: PP -> AS ) ;</li>
<li> ( </li>
<ul>
	<li> ( <strong>buy_flights</strong>: AS -> CA<sub><em>i</em></sub> ; <strong>buy_flights_res</strong>: CA<sub><em>i</em></sub> -> AS ) ;</li>  
	<li> ( </li>
<ul>
		<li>(</li>  
		    <!-- Calcolo distanza aeroporto/casa -->
<ul>
			<li> ( <strong>calc_dist</strong>: AS -> DG ; <strong>calc_dist_res</strong>: DG -> AS ) ;</li>  
			<li> ( </li>
<ul>
				<!-- Calcolo distanze per identificare la compagnia di trasporto più vicina -->
				<li> ( <strong>calc_dist</strong>: AS -> DG ; <strong>calc_dist_res</strong>: DG -> AS )<sup>*</sup> ;</li>  
				<li> ( <strong>pren_trs</strong>: AS -> CT<sub><em>j</em></sub> ; <strong>pren_trs_res</strong>: CT<sub><em>j</em></sub> -> AS) </li>
</ul>
			<li> ) </li> 
			<li> + 1 </li>
</ul>
		<li> ) </li> 
		<li> + 1 </li>
</ul>
	<li> ); </li>    
    <li> <strong>send_tickets</strong>: AS -> UT </li>
</ul>

<li> ) </li>  
<li> + <strong>payment_failure</strong>: AS -> UT </li>
</ul>
)  <br>
+ <strong>ins_code_failure</strong>: AS -> UT

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

### AS (ACMESky)

proj(**ProcessoRegistrazioneInteresseUtente**, AS) =  
( **reg**@UT ; <span style="text-decoration: overline">**reg_res**</span>@UT )

proj(**VerificaGiornaliera**, AS) =   
(  
- (<span style="text-decoration: overline">**control**</span>@CA<sub>*1*</sub> ; **control_res**@CA<sub>*1*</sub> ) |  
- ... |  
- (<span style="text-decoration: overline">**control**</span>@CA<sub>*N*</sub> ; **control_res**@CA<sub>*N*</sub> )  

) ;  
(  
- ( <span style="text-decoration: overline">**notify**</span>@PG ; 1 ) + 1  

)

proj(**NotificaVoliLastMinute**, AS) =  
( **last_minute**@CA<sub>*i*</sub> ) ;  
(  
- ( <span style="text-decoration: overline">**notify**</span>@PG ; 1 ) + 1  

)

proj(**AcquistoOfferta**, AS) =  
( **ins_code**@UT ) ;  
(    
- ( <span style="text-decoration: overline">**req_pay**</span>@PP ; 1 ; 1 ; **req_pay_res**@AS ) ;  
- (    
	- ( <span style="text-decoration: overline">**buy_flights**</span>@CA<sub>*i*</sub> ; **buy_flights_res**@CA<sub>*i*</sub> ) ;  
	- (
		- (
			- ( <span style="text-decoration: overline">**calc_dist**</span>@DG ; **calc_dist_res**@DG ) ;  
			- (  
				- ( <span style="text-decoration: overline">**calc_dist**</span>@DG ; **calc_dist_res**@DG )<sup>\*</sup>
				- ( <span style="text-decoration: overline">**pren_trs**</span>@CT<sub>*j*</sub> ; **pren_trs_res**@CT<sub>*j*</sub> )

			- )
			- \+ 1

		- )
		- \+ 1

	- );  
	- <span style="text-decoration: overline">**send_tickets**</span>@UT

- )
- \+ <span style="text-decoration: overline">**payment_failure**</span>@UT

)  
\+ <span style="text-decoration: overline">**ins_code_failure**</span>@UT

### UT (UTente)

proj(**ProcessoRegistrazioneInteresseUtente**, UT) =  
( <span style="text-decoration: overline">**reg**</span>@AS ; **reg_res**@AS )

proj(**VerificaGiornaliera**, UT) =  
(  
- ( 1 ; 1 ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(  
- ( 1 ; **message**@PG ) + 1  

)  
= **message**@PG

proj(**NotificaVoliLastMinute**, UT) =  
( 1 ) ;  
(  
- ( 1 ; **message**@PG ) + 1  

)  
= **message**@PG

proj(**AcquistoOfferta**, UT) =  
( <span style="text-decoration: overline">**ins_code**</span>@AS ) ;  
(  
- ( 1 ; **pay_offer**@PP ; <span style="text-decoration: overline">**pay_offer_res**</span>@PP ; 1 ) ;  
- (    
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( 1 ; 1 )
			- (  
				- ( 1 ; 1 )<sup>\*</sup>
				- ( 1 ; 1 )

			- )
			- \+ 1

		- )
		- \+ 1

	- ) ;
	- **send_tickets**@AS

- )
- \+ **payment_failure**@AS

)
\+ **ins_code_failure**@AS

### DG (Distanze Geografiche)

proj(**ProcessoRegistrazioneInteresseUtente**, DG) =  
( 1 ; 1 ) = 1

proj(**VerificaGiornaliera**, DG) =  
(  
- (1 ; 1) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(  
- ( 1 ; 1 ) + 1  

)  
= 1

proj(**NotificaVoliLastMinute**, DG) =  
( 1 ) ;  
(  
- ( 1 ; 1 ) + 1  

)  
= 1

proj(**AcquistoOfferta**, DG) =  
( 1 ) ;  
(    
- ( 1 ; 1 ; 1 ; 1 ) ;  
- (    
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( **calc_dist**@AS ; <span style="text-decoration: overline">**calc_dist_res**</span>@AS ) ;  
			- (  
				- ( **calc_dist**</span>@AS ; <span style="text-decoration: overline">**calc_dist_res**</span>@AS )<sup>\*</sup>
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

proj(**ProcessoRegistrazioneInteresseUtente**, PP) =  
( 1 ; 1 ) = 1

proj(**VerificaGiornaliera**, PP) =  
(  
- ( 1 ; 1 ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(  
- ( 1 ; 1 ) + 1  

) = 1

proj(**NotificaVoliLastMinute**, PP) =  
( 1 ) ;  
(  
- ( 1 ; 1 ) + 1  

) = 1

proj(**AcquistoOfferta**, PP) =  
( 1 ) ;  
(    
- ( **req_pay**@AS ; <span style="text-decoration: overline">**pay_offer**</span>@UT ; **pay_offer_res**@UT ; <span style="text-decoration: overline">**req_pay_res**@AS</span> ) ;  
- (    
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( 1 ; 1 ) ;  
			- (  
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

proj(**ProcessoRegistrazioneInteresseUtente**, PG) =  
( 1 ; 1 ) = 1

proj(**VerificaGiornaliera**, PG) =  
(  
- ( 1 ; 1 ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(  
- ( **notify**@AS ; <span style="text-decoration: overline">**message**</span>@UT ) + 1  

) = ( **notify**@AS ; <span style="text-decoration: overline">**message**</span>@UT )  

proj(**NotificaVoliLastMinute**, PG) =  
( 1 ) ;  
(  
- ( **notify**@AS ; <span style="text-decoration: overline">**notify**</span>@UT ) + 1  
 
) = ( **notify**@AS ; <span style="text-decoration: overline">**notify**</span>@UT )

proj(**AcquistoOfferta**, PG) =  
( 1 ) ;  
(    
- ( 1 ; 1 ; 1 ; 1 ) ;  
- (    
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( 1 ; 1 ) ;  
			- (  
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

### CT<sub>*j*</sub> (Compagnia Trasporti)

proj(**ProcessoRegistrazioneInteresseUtente**, CT<sub>*j*</sub>) =  
( 1 ; 1 ) = 1

proj(**VerificaGiornaliera**, CT<sub>*j*</sub>) =  
(  
- ( 1 ; 1 ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(  
- ( 1 ; 1 ) + 1  

) = 1

proj(**NotificaVoliLastMinute**, CT<sub>*j*</sub>) =  
( 1 ) ;  
(  
- ( 1 ; 1 ) + 1  

) = 1

proj(**AcquistoOfferta**, CT<sub>*j*</sub>) =  
( 1 ) ;  
(    
- ( 1 ; 1 ; 1 ; 1 ) ;  
- (    
	- ( 1 ; 1 ) ;  
	- (
		- (
			- ( 1 ; 1 ) ;  
			- (  
				- ( 1 ; 1 )<sup>\*</sup>
				- ( **pren_trs**</span>@AS; <span style="text-decoration: overline">**pren_trs_res**</span>@AS )

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

### CA<sub>*i*</sub> (Compagnia Aerea)

proj(**ProcessoRegistrazioneInteresseUtente**, CA<sub>*i*</sub>) =  
( 1 ; 1 ) = 1

proj(**VerificaGiornaliera**, CA<sub>*i*</sub>) =  
(  
- (1 ; 1) |  
- ... |  
- ( **control**@AS ; <span style="text-decoration: overline">**control_res**</span>@AS ) |  
- ... |  
- ( 1 ; 1 )  

) ;  
(  
- ( 1 ; 1 ) + 1  

) = ( **control**@AS ; <span style="text-decoration: overline">**control_res**</span>@AS )

proj(**NotificaVoliLastMinute**, CA<sub>*i*</sub>) =  
( <span style="text-decoration: overline">**last_minute**</span>@AS ) ;  
(  
- ( 1 ; 1 ) + 1  

) = <span style="text-decoration: overline">**last_minute**</span>@AS

proj(**AcquistoOfferta**, CA<sub>*i*</sub>) =  
( 1 ) ;  
(    
- ( 1 ; 1 ; 1 ; 1 ) ;  
- (    
	- ( **buy_flights**@AS ; <span style="text-decoration: overline">**buy_flights_res**</span>@CA ) ;  
	- (
		- (
			- ( 1 ; 1 ) ;  
			- (  
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
