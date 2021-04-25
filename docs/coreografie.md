---
hide:
  - navigation
---

<style>
    div.choreographies ul li,
    div.projections ul li {
        list-style-type: none;
	}

	div.choreographies ul, div.projections ul {
		margin: 0px;
	}
</style>
# Coreografie, ruoli e proiezioni delle coreografie sui ruoli

## Coreografie

Le seguenti coreografie modellano tutti quanti i possibili processi che possono avvenire nel sistema che verrà implementato.  
Le interazioni che non comprendono *ACMESky* come mittente o destinatario sono molto semplificate, in quanto esterne ad essa e di cui non possiamo conoscere l'implementazione interna.

Verranno utilizzate le seguenti sigle per i ruoli delle coreografie:

- AS: ACMESky;
- CA<sub>*i*</sub>: Compagnie Aerea *i*, con *i* &isin; {1..N};
- CT<sub>*j*</sub>: Compagnie Trasporto *j*, con *j* &isin; {1..M};
- PG: ProntoGram;
- PP: Provider dei Pagamenti;
- DG: servizio per il calcolo delle Distanze Geografiche;
- UT<sub>*k*</sub>: UTente (il range in cui *k* varia dipende dagli utenti che attualmente hanno registrato interessi presso ACMESky).

### Registrazione interesse di un utente

<div class="choreographies">
<p>
<strong>RegistrazioneInteresseUtente</strong> ::= (<strong>reg</strong>: UT<sub><em>k</em></sub> -> AS; <strong>reg_res</strong>: AS -> UT<sub><em>k</em></sub>)
</p>
</div>

Le interazioni hanno il seguente significato:

- **reg**: registrazione dell'interesse di un utente per un viaggio;
- **reg_res**: risposta all'interazione **reg** (conferma o smentita della registrazione).

### Verifica giornaliera delle offerte

<div class="choreographies">

<p>
<strong>VerificaGiornaliera</strong> ::=<br />
(
<ul>
    <li> ( <strong>control</strong>: AS -> CA<sub><em>1</em></sub> ; <strong>control_res</strong>: CA<sub><em>1</em></sub> -> AS ) | </li>
    <li> ... |</li>
    <li> ( <strong>control</strong>: AS -> CA<sub><em>N</em></sub> ; <strong>control_res</strong>: CA<sub><em>N</em></sub> -> AS )</li>
</ul>
) ;<br />
(
<ul>
    <li> ( ( <strong>notify</strong>: AS -> PG ; <strong>message</strong>: PG -> UT<sub><em>1</em></sub> ) + 1 ) |</li>
    <li> ... |</li>
    <li> ( ( <strong>notify</strong>: AS -> PG ; <strong>message</strong>: PG -> UT<sub><em>N</em></sub> ) + 1 ) </li>
</ul>
)
</p>
</div>

Le interazioni hanno il seguente significato:

- **control**: controllo giornaliero della presenza di offerte da parte della *compagnia aerea*;
- **control_res**: risposta all'interazione **control** (inoltro delle nuove offerte disponibili);
- **notify**: notifica della presenza di voli di interesse per l'utente tramite *ProntoGram* (possono esserci come no, se non ci sono l'utente non viene contattato). Il codice offerta inviato è univoco per offerta: se ci sono più utenti con gli stessi interessi viene inviato lo stesso codice offerta;
- **message**: *ProntoGram* riferisce il messaggio all'utente (essendo un servizio esterno questa è una semplificazione).

### Ricezione offerte last minute

<div class="choreographies">

<p>
<strong>NotificaVoliLastMinute</strong> ::=<br />
( <strong>last_minute</strong>: CA<sub><em>i</em></sub> -> AS ) ;<br />
(  
<ul>
    <li> ( ( <strong>notify</strong>: AS -> PG ; <strong>message</strong>: PG -> UT<sub><em>1</em></sub> ) + 1 ) |</li>
    <li> ... |</li>
    <li> ( ( <strong>notify</strong>: AS -> PG ; <strong>message</strong>: PG -> UT<sub><em>N</em></sub> ) + 1 ) </li>
</ul>
)
</p>
</div>

Le interazioni hanno il seguente significato:

- **last_minute**: la *compagnia aerea* notifica *ACMESky* della presenza di alcune offerte last minute, inviandogliele;
- **notify**: notifica della presenza di voli di interesse per l'utente tramite *ProntoGram* (possono esserci come no, se non ci sono l'utente non viene contattato). Il codice offerta inviato è univoco per offerta: se ci sono più utenti con gli stessi interessi viene inviato lo stesso codice offerta;
- **message**: *ProntoGram* riferisce il messaggio all'utente (essendo un servizio esterno questa è una semplificazione).

### Acquisto offerta da un utente

<div class="choreographies">

<p>
<strong>AcquistoOfferta</strong> ::=  <br />
( <strong>ins_code</strong>: UT<sub><em>k</em></sub> -> AS ; <strong>ins_code_res</strong>: AS -> UT<sub><em>k</em></sub>) ;<br />  
(  
<ul>
    <li>( <strong>req_pay</strong>: AS -> PP ; <strong>req_pay_res</strong>: PP -> AS ;  <strong>send_payment_ref</strong>: AS -> UT<sub><em>k</em></sub> ; <strong>pay_offer</strong>: UT<sub><em>k</em></sub> -> PP ; ( <strong>pay_offer_res</strong> PP -> UT<sub><em>k</em></sub> | <strong>send_payment_status</strong>: PP -> AS ) ) ;</li>
    <li>( </li>
    <li><ul>
    	<li>( <strong>buy_flights</strong>: AS -> CA<sub><em>i</em></sub> ; <strong>buy_flights_res</strong>: CA<sub><em>i</em></sub> -> AS ) ;</li>  
    	<li>(</li>
        <li><ul>
            <li>(</li>  
            <li><ul>
                <!-- Calcolo distanza aeroporto/casa -->
                <li>( <strong>calc_dist</strong>: AS -> DG ; <strong>calc_dist_res</strong>: DG -> AS ) ;</li>  
                <li>(</li>
                <li><ul>
                    <!-- Calcolo distanze per identificare la compagnia di trasporto più vicina -->
                    <li> ( <strong>calc_dist</strong>: AS -> DG ; <strong>calc_dist_res</strong>: DG -> AS )<sup>*</sup> ;</li>  
                    <li> ( <strong>pren_trs</strong>: AS -> CT<sub><em>j</em></sub> ; <strong>pren_trs_res</strong>: CT<sub><em>j</em></sub> -> AS)</li>
                </ul></li>
                <li>)</li> 
                <li> + 1</li>
            </ul></li>
            <li>)</li> 
            <li>+ 1</li>
        </ul></li>
        <li>);</li>    
        <li><strong>send_tickets</strong>: AS -> UT<sub><em>k</em></sub> </li>
    </ul></li>
    <li>) </li>  
    <li> + <strong>payment_failure</strong>: AS -> UT<sub><em>k</em></sub> </li>
</ul>
)<br />
+ <strong>ins_code_failure</strong>: AS -> UT<sub><em>k</em></sub>
</p>
</div>

Le interazioni hanno il seguente significato:

- **ins_code**: l'utente inserisce il codice ricevuto via *ProntoGram* sul portale;
- **ins_code_res**: risposta all'interazione **ins_code** (conferma o smentita della ricezione del codice offerta);
- **req_pay**: *ACMESky* richiede il pagamento al *Provider dei Pagamenti*;
- **req_pay_res**: risposta all'interazione **req_pay** (inoltro riferimento per il pagamento dell'offerta);
- **send_payment_ref**: *ACMESky* invia all'utente il riferimento per poter pagare l'offerta tramite il *Provider dei Pagamenti*;
- **pay_offer**: l'utente invia i dati per il pagamento al *Provider dei Pagamenti*;
- **pay_offer_res**: risposta all'interazione **pay_offer** (invio esito del pagamento all'utente);
- **send_payment_status**: il *Provider dei Pagamenti* comunica ad *ACMESky* l'esito del pagamento;
- **buy_flights**: *ACMESky* compra per conto dell'utente il biglietto per i voli scelti presso la *compagnia aerea* CA<sub>*i*</sub> che fornisce entrambi i voli che soddisfano il bisogno;
- **buy_flights_res**: risposta all'interazione **buy_flights** (CA<sub>*i*</sub> conferma ad *ACMESky* la disponibilità del volo e inoltra il biglietto);
- **calc_dist**: *ACMESky* richiede il calcolo della distanza al servizio esterno per il calcolo delle *distanze geografiche*;
- **calc_dist_res**: risposta all'interazione **calc_dist**;
- **pren_trs**: in base alla distanza *ACMESky* può, oppure no, prenotare il trasporto;
- **pren_trs_res**: risposta all'interazione **pren_trs**;
- **send_tickets**: *ACMESky* invia all'utente i biglietti;
- **payment_failure**: *ACMESky* comunica all'utente che c'è stato un problema durante il pagamento;
- **ins_code_failure**: *ACMESky* comunica all'utente che il codice inserito non è valido.

## Verifica della connectedness delle coreografie

### Registrazione interesse di un utente
**RegistrazioneInteresseUtente** è connessa in quanto il ricevente in **reg** è uguale al mittente in **reg_res**.

### Verifica giornaliera delle offerte
In **VerificaGiornaliera**, la singola comunicazione fra AS e un qualsiasi CA<sub>*i*</sub> è connessa come per la precedente coreografia, in quanto il ricevente in **control** è uguale al mittente in **control_res**. Le interazioni parallele non hanno condizioni per poter verificare la connectedness e quindi non vengono verificate.  
Per quanto riguarda la composizione sequenziale fra la prima parte della coreografia, in cui avvengono le interazioni ( **control** ; **control_res** ) fra AS e le varie CA<sub>*i*</sub>, e la seconda parte, in cui avviene l'interazione dei **notify** fra AS e PG, è nuovamente assicurata la connectedness. Questo poiché ogni interazione all'interno della composizione parallela termina con ricevente AS e il mittente di ogni **notify** è AS.  
Dopo **notify** nella sequenza c'è **message** che invia il messaggio ad UT<sub>*k*</sub>. Essendo PG il ricevente di **notify** e il mittente di **message**, la sequenza è connessa. Nel caso in cui non avvenga **notify** fra AS e PG poiché non vi sono voli per l'utente da comunicare via ProntoGram, allora viene eseguito il ramo "1" (ovvero non viene fatto nulla). In questo caso, PG rimane in attesa di **notify** ma è corretto poiché è il suo compito.  
Come nell'interazione fra AS e le varie CA<sub>*i*</sub>, le interazioni parallele ( ( **notify** ; **message** ) + 1 ) non hanno condizioni per verificare la connectedness e quindi non viene verificata.

### Ricezione offerte last minute
**NotificaVoliLastMinute** è connessa in modo non dissimile a quanto avviene in **VerificaGiornaliera**. AS è ricevente in **last_minute** e mittente in **notify**, dopodiché PG è ricevente in **notify** e mittente in **message**. Da notare che **notify** e **message**, avvengono solamente se ci sono voli di interesse per l'utente e quindi nuovamente, in caso non ce ne siano, *ProntoGram* resterebbe in attesa ma non sarebbe un problema poiché è il suo compito. Come nella precedente coreografia, per le interazioni parallele ( ( **notify** ; **message** ) + 1 ) non può essere verificata la connectedness.

### Acquisto offerta da un utente
**AcquistoOfferta** è connessa perché:

- il ricevente di **ins_code** è il mittente di **ins_code_res** e di **ins_code_failure** (quest'ultimo termina la coreografia);
- il mittente di **ins_code_res** è lo stesso mittente di **req_pay**;
- il ricevente di **req_pay** è il mittente di **req_pay_res**;
- il ricevente di **req_pay_res** è il mittente di **send_payment_ref**;
- il ricevente di **send_payment_ref** è il mittente di **pay_offer**;
- il ricevente di **pay_offer** è sia il mittente di **pay_offer_res** che di **send_payment_status** e i due riceventi sono diversi (UT<sub><em>k</em></sub> in un caso, AS nell'altro);
- il ricevente di **send_payment_status** è il mittente di **buy_flights** e di **payment_failure** (quest'ultimo termina la coreografia);
- il ricevente di **buy_flights** è il mittente di **buy_flights_res**;
- il ricevente di **buy_flights_res** è il mittente di **calc_dist** e di **send_tickets** (quest'ultimo termina la coreografia);
- il ricevente di **calc_dist** è il mittente di **calc_dist_res** (nella sua prima invocazione);
- il ricevente di **calc_dist_res** è il mittente della seconda invocazione di **calc_dist** e di **send_tickets** (quest'ultimo termina la coreografia);
- il ricevente di **calc_dist** è il mittente di **calc_dist_res** nella prima parte della sequenza ( **calc_dist** ; **calc_dist_res** )* ;
- il ricevente di **calc_dist_res** è il mittente di **calc_dist** nella seconda parte della sopracitata sequenza, che quindi è connessa;
- il ricevente di **calc_dist_res** (l'ultimo della sequenza lunga almeno uno per ipotesi) è il mittente di **pren_trs**;
- il ricevente di **pren_trs** è il mittente di **pren_trs_res**;
- il ricevente di **pren_trs_res** è il mittente di **send_tickets** (quest'ultimo termina la coreografia).

## Proiezioni
Seguono le proiezioni delle coreografie, divise per ruolo.

### AS (ACMESky)
<div class="projections">
<p>
proj(<strong>RegistrazioneInteresseUtente</strong>, AS) =<br />
( <strong>reg</strong>@UT<sub><em>k</em></sub> ; <span style="text-decoration: overline"><strong>reg_res</strong></span>@UT<sub><em>k</em></sub> )
</p>

<p>
proj(<strong>VerificaGiornaliera</strong>, AS) =<br /> 
(
<ul>
<li>(<span style="text-decoration: overline"><strong>control</strong></span>@CA<sub><em>1</em></sub> ; <strong>control_res</strong>@CA<sub><em>1</em></sub> ) |</li>
<li>... |</li>
<li>(<span style="text-decoration: overline"><strong>control</strong></span>@CA<sub><em>N</em></sub> ; <strong>control_res</strong>@CA<sub><em>N</em></sub> )</li>
</ul>
) ;<br />
(
<ul>
    <li>( ( <span style="text-decoration: overline"><strong>notify</strong></span>@PG ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( <span style="text-decoration: overline"><strong>notify</strong></span>@PG ; 1 ) + 1 )</li>
</ul>
)
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, AS) =<br />
( <strong>last_minute</strong>@CA<sub><em>i</em></sub> ) ;<br />
(
<ul>
    <li>( ( <span style="text-decoration: overline"><strong>notify</strong></span>@PG ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( <span style="text-decoration: overline"><strong>notify</strong></span>@PG ; 1 ) + 1 )</li>
</ul>
)
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, AS) =<br />
( <strong>ins_code</strong>@UT<sub><em>k</em></sub> ; <span style="text-decoration: overline"><strong>ins_code_res</strong></span>@UT<sub><em>k</em></sub>) ;<br />
(<br />
<ul>
<li>( <span style="text-decoration: overline"><strong>req_pay</strong></span>@PP ; <strong>req_pay_res</strong>@PP ; <span style="text-decoration: overline"><strong>send_payment_ref</strong></span>@UT<sub><em>k</em></sub> ; 1 ; ( 1 | <strong>send_payment_status</strong>@PP ) ;</li>
<li>( </li>
<li><ul>
	<li>( <span style="text-decoration: overline"><strong>buy_flights</strong></span>@CA<sub><em>i</em></sub> ; <strong>buy_flights_res</strong>@CA<sub><em>i</em></sub> ) ;</li>
	<li>(</li>
<li>	<ul>
		<li>(</li>
	<li>	<ul>
			<li>( <span style="text-decoration: overline"><strong>calc_dist</strong></span>@DG ; <strong>calc_dist_res</strong>@DG ) ;</li>
			<li>(</li>
		<li>	<ul>
				<li>( <span style="text-decoration: overline"><strong>calc_dist</strong></span>@DG ; <strong>calc_dist_res</strong>@DG )<sup>*</sup></li>
				<li>( <span style="text-decoration: overline"><strong>pren_trs</strong></span>@CT<sub><em>j</em></sub> ; <strong>pren_trs_res</strong>@CT<sub><em>j</em></sub> )</li>
</ul></li>
			<li>)</li>
			<li>+ 1</li>
</ul></li>
		<li>)</li>
		<li>+ 1</li>
</ul></li>
	<li>);</li>
	<li><span style="text-decoration: overline"><strong>send_tickets</strong></span>@UT<sub><em>k</em></sub></li>
</ul></li>
<li>)</li>
<li>+ <span style="text-decoration: overline"><strong>payment_failure</strong></span>@UT<sub><em>k</em></sub></li>
</ul>
)<br />
+ <span style="text-decoration: overline"><strong>ins_code_failure</strong></span>@UT<sub><em>k</em></sub>
</p>
</div>

### UT<sub><em>k</em></sub> (UTente)
<div class="projections">
<p>
proj(<strong>RegistrazioneInteresseUtente</strong>, UT<sub><em>k</em></sub>) =<br />
( <span style="text-decoration: overline"><strong>reg</strong></span>@AS ; <strong>reg_res</strong>@AS )
</p>

<p>
proj(<strong>VerificaGiornaliera</strong>, UT<sub><em>k</em></sub>) =<br />
(<br />
<ul>
<li> ( 1 ; 1 ) |</li>  
<li>... |</li> 
<li> ( 1 ; 1 )</li>  
</ul>
) ;  <br />
(<br />
<ul>
    <li> ( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li> ( ( 1 ; <strong>message</strong>@PG ) + 1) |</li>
    <li>... |</li>
    <li> ( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= <strong>message</strong>@PG
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, UT<sub><em>k</em></sub>) =<br />
( 1 ) ;<br />
(
<ul>
    <li> ( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li> ( ( 1 ; <strong>message</strong>@PG ) + 1) |</li>
    <li>... |</li>
    <li> ( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= <strong>message</strong>@PG
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, UT<sub><em>k</em></sub>) =<br />
( <span style="text-decoration: overline"><strong>ins_code</strong></span>@AS ; <strong>ins_code_res</strong>@AS ) ;<br />
(
<ul>
<li>( 1 ; 1 ; <strong>send_payment_ref</strong>@AS ; <span style="text-decoration: overline"><strong>pay_offer</strong></span>@PP ; ( <strong>pay_offer_res</strong>@PP | 1 ) ) ;</li>  
<li>(</li>
<li><ul>
	<li> ( 1 ; 1 ) ;</li>  
	<li>(</li>
<li><ul>
		<li>(</li>
<li><ul>
			<li>( 1 ; 1 )</li> 
			<li> (</li>
<li><ul>
				<li>( 1 ; 1 )<sup>*</sup></li>
				<li>( 1 ; 1 )</li> 
</ul></li>
			<li>)</li>
			<li>+ 1</li>
</ul></li>
		<li>)</li>
		<li>+ 1</li>
</ul></li>
	<li>) ;</li>
	<li><strong>send_tickets</strong>@AS</li>
</ul></li>
<li>)</li> 
<li>+ <strong>payment_failure</strong>@AS</li>
</ul>
)<br/>
+ <strong>ins_code_failure</strong>@AS<br />
= (  <span style="text-decoration: overline"><strong>ins_code</strong></span>@AS ; <strong>ins_code_res</strong>@AS ) ; ( <strong>send_payment_ref</strong>@AS ; <span style="text-decoration: overline"><strong>pay_offer</strong></span>@PP ; <strong>pay_offer_res</strong>@PP ) ;
</p>
</div>
### DG (Distanze Geografiche)
<div class="projections">
<p>
proj(<strong>RegistrazioneInteresseUtente</strong>, DG) =<br />
( 1 ; 1 )<br />
= 1
</p>

<p>
proj(<strong>VerificaGiornaliera</strong>, DG) =<br />
(
<ul>
<li>(1 ; 1) |</li>  
<li>... |</li>   
<li>( 1 ; 1 )</li>  
</ul>
) ;<br />
(
<ul>
    <li>( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= 1
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, DG) =<br />
( 1 ) ;<br />  
(
<ul>
    <li>( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= 1
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, DG) =<br />
( 1 ; 1 ) ;<br />
(
<ul>
<li>( 1 ; 1 ; 1 ; 1 ; ( 1 | 1 ) ) ;</li>  
<li>(</li>
<li><ul>
	<li>( 1 ; 1 ) ;</li>  
	<li>(</li>
<li><ul>
		<li>(</li>
<li><ul>
			<li>( <strong>calc_dist</strong>@AS ; <span style="text-decoration: overline"><strong>calc_dist_res</strong></span>@AS ) ;</li>  
			<li>(</li>
<li><ul>
				<li>( <strong>calc_dist</strong></span>@AS ; <span style="text-decoration: overline"><strong>calc_dist_res</strong></span>@AS )<sup>*</sup></li>
				<li>( 1 ; 1 )</li>
</ul></li>
			<li>)</li>
			<li>+ 1</li>
</ul></li>
		<li>)</li>
		<li>+ 1</li>
</ul></li>
	<li>);</li>  
	<li>+ 1</li>
</ul></li>
<li>)</li>
<li>+ 1</li>
</ul>
)<br />
+ 1<br />
= ( <strong>calc_dist</strong>@AS ; <span style="text-decoration: overline"><strong>calc_dist_res</strong></span>@AS ) ; ( <strong>calc_dist</strong></span>@AS ; <span style="text-decoration: overline"><strong>calc_dist_res</strong></span>@AS )<sup>*</sup> ) + 1
</p>
</div>

### PP (Provider dei Pagamenti)
<div class="projections">

<p>
proj(<strong>RegistrazioneInteresseUtente</strong>, PP) =<br />
( 1 ; 1 )<br />
= 1
</p>

<p>
proj(<strong>VerificaGiornaliera</strong>, PP) =<br />
(
<ul>
<li>( 1 ; 1 ) |</li>  
<lI> ... |</lI>  
<li>( 1 ; 1 )</li>  
</ul>
) ;<br/>  
(
<ul>
    <li>( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= 1
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, PP) =<br />
( 1 ) ;<br/>  
(
<ul>
    <li>( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= 1
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, PP) =<br />
( 1 ; 1 ) ; <br/>
(
<ul>
<li>( <strong>req_pay</strong>@AS ; <span style="text-decoration: overline"><strong>req_pay_res</strong></span>@AS ; 1 ; <strong>pay_offer</strong>@UT<sub><em>k</em></sub> ; ( <span style="text-decoration: overline"><strong>pay_offer_res</strong></span>@UT<sub><em>k</em></sub> | <span style="text-decoration: overline"><strong>send_payment_status</strong></span>@AS ) ) ;</li>  
<li>(</li>
<li><ul>
	<li>( 1 ; 1 ) ;</li>  
	<li>(</li>
<li><ul>
		<li>(</li>
<li><ul>
			<li>( 1 ; 1 ) ;</li>  
			<li>(</li>
<li><ul>
				<li>( 1 ; 1 )<sup>*</sup></li>
				<li>( 1 ; 1 )</li>
</ul></li>
			<li>)</li>
			<li>+ 1</li>
</ul></li>
		<li>)</li>
		<li>+ 1</li>
</ul></li>
	<li>);</li>  
	<li>+ 1</li>
</ul></li>
<li>)</li>
<li>+ 1</li>
</ul>
)<br/>
+ 1<br />

= ( <strong>req_pay</strong>@AS ; <span style="text-decoration: overline"><strong>req_pay_res</strong></span>@AS ; <strong>pay_offer</strong>@UT<sub><em>k</em></sub> ; ( <span style="text-decoration: overline"><strong>pay_offer_res</strong></span>@UT<sub><em>k</em></sub> | <span style="text-decoration: overline"><strong>send_payment_status</strong></span>@AS ) )
</p>
</div>

### PG (ProntoGram)
<div class="projections">
<p>
proj(<strong>RegistrazioneInteresseUtente</strong>, PG) =<br />
( 1 ; 1 )<br />
= 1
</p>

<p>
proj(<strong>VerificaGiornaliera</strong>, PG) =<br />
(
<ul>
    <li>( 1 ; 1 ) |</li>
    <li>... |</li>
    <li>( 1 ; 1 )</li>
</ul>
) ;<br />
(
<ul>
    <li>( ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>1</em></sub> ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>N</em></sub> ) + 1 )</li>
</ul>
)<br />
= ( ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>1</em></sub> ) + 1 ) | ... | ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>k</em></sub> ) | ... | ( ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>N</em></sub> ) + 1 ) 
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, PG) =<br />
( 1 ) ;<br />
(
<ul>
    <li>( ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>1</em></sub> ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>N</em></sub> ) + 1 )</li>
</ul>
)<br />
= ( ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>1</em></sub> ) + 1 ) | ... | ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>k</em></sub> ) | ... | ( ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT<sub><em>N</em></sub> ) + 1 ) 
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, PG) =<br />
( 1 ; 1 ) ;<br />
(
<ul>
    <li>( 1 ; 1 ; 1 ; 1 ; ( 1 | 1 ) ) ;</li>
    <li>(</li>
    <li><ul>
        <li>( 1 ; 1 ) ;</li>
        <li>(</li>
        <li><ul>
            <li>(</li>
            <li><ul>
                <li>( 1 ; 1 ) ;</li>
                <li>(</li>
                <li><ul>
                    <li>( 1 ; 1 )<sup>*</sup></li>
                    <li>( 1 ; 1 )</li>
				</ul></li>
                <li>)</li>
                <li>+ 1</li>
            </ul></li>
            <li>)</li>
            <li>+ 1</li>
        </ul></li>
        <li>);</li>
        <li>+ 1</li>
	</ul></li>
    <li>)</li>
    <li>+ 1</li>
</ul>
)<br /> 
+ 1<br />
= 1
</p>
</div>

### CT<sub><em>j</em></sub> (Compagnia Trasporti)
<div class="projections">
<p>
proj(<strong>RegistrazioneInteresseUtente</strong>, CT<sub><em>j</em></sub>) =<br />
( 1 ; 1 )<br />
= 1
</p>

<p>
proj(<strong>VerificaGiornaliera</strong>, CT<sub><em>j</em></sub>) =<br />
(
<ul>
    <li>( 1 ; 1 ) |</li>
    <li>... |</li>
    <li>( 1 ; 1 )</li> 
</ul>
) ;<br />
(
<ul>
    <li>( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= 1
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, CT<sub><em>j</em></sub>) =<br />
( 1 ) ;<br />
(
<ul>
    <li>( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= 1
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, CT<sub><em>j</em></sub>) =<br />
( 1 ; 1 ) ;<br /> 
(
<ul>
    <li>( 1 ; 1 ; 1 ; 1 ; ( 1 | 1 ) ;</li>
    <li>(</li>
    <li><ul>
		<li>( 1 ; 1 ) ;</li>
        <li>(</li>
        <li><ul>
            <li>(</li>
            <li><ul>
                <li>( 1 ; 1 ) ;</li>  
                <li>(</li>
                <li><ul>
                    <li>( 1 ; 1 )<sup>*</sup></li>
                    <li>( <strong>pren_trs</strong></span>@AS; <span style="text-decoration: overline"><strong>pren_trs_res</strong></span>@AS )</li>
                </ul></li>
                <li>)</li>
                <li>+ 1</li>
            </ul></li>
            <li>)</li>
            <li>+ 1</li>
		</ul></li>
        <li>);</li>
        <li>+ 1</li>
    </ul></li>
    <li>)</li>
    <li>+ 1</li>
</ul>
)<br />
+ 1<br />
= ( <strong>pren_trs</strong></span>@AS; <span style="text-decoration: overline"><strong>pren_trs_res</strong></span>@AS )
</p>
</div>

### CA<sub><em>i</em></sub> (Compagnia Aerea)

<div class="projections">
<p>
proj(<strong>RegistrazioneInteresseUtente</strong>, CA<sub><em>i</em></sub>) =<br />
( 1 ; 1 )<br />
= 1
</p>

<p>
proj(<strong>VerificaGiornaliera</strong>, CA<sub><em>i</em></sub>) =<br />
(
<ul>
    <li>(1 ; 1) |</li>
    <li>... |</li>
    <li>( <strong>control</strong>@AS ; <span style="text-decoration: overline"><strong>control_res</strong></span>@AS ) |</li>  
    <li>... |</li>
    <li>( 1 ; 1 )</li>
</ul>
) ;<br /> 
(
<ul>
    <li>( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= ( <strong>control</strong>@AS ; <span style="text-decoration: overline"><strong>control_res</strong></span>@AS )
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, CA<sub><em>i</em></sub>) =<br />
( <span style="text-decoration: overline"><strong>last_minute</strong></span>@AS ) ;<br />
(
<ul>
    <li>( ( 1 ; 1 ) + 1 ) |</li>
    <li>... |</li>
    <li>( ( 1 ; 1 ) + 1 )</li>
</ul>
)<br />
= <span style="text-decoration: overline"><strong>last_minute</strong></span>@AS
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, CA<sub><em>i</em></sub>) =<br />
( 1 ; 1 ) ;<br />
(
<ul>
    <li>( 1 ; 1 ; 1 ; 1 ; ( 1 | 1  )) ;</li>
    <li>(</li>
    <li><ul>
        <li>( <strong>buy_flights</strong>@AS ; <span style="text-decoration: overline"><strong>buy_flights_res</strong></span>@CA ) ;</li>
        <li>(</li>
        <li><ul>
            <li>(</li>
            <li><ul>
                <li>( 1 ; 1 ) ;</li>
                <li>(</li>
                <li><ul>
                    <li>( 1 ; 1 )<sup>*</sup></li>
                    <li>( 1 ; 1 )</li>
                </ul></li>
                <li>)</li>
                <li>+ 1</li>
            </ul></li>
            <li>)</li>
            <li>+ 1</li>
        </ul></li>
        <li>);</li>  
        <li>+ 1</li>
    </ul></li>
    <li>)</li>
    <li>+ 1</li>
</ul>
)<br />
+ 1<br />
= ( <strong>buy_flights</strong>@AS ; <span style="text-decoration: overline"><strong>buy_flights_res</strong></span>@CA )
</p>
</div>
