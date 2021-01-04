<style>
    div#choreographies ul li,
    div#projections ul li {
        list-style-type: none;
	}

	div#choreographies ul, div#projections ul {
		margin: 0px;
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

<p>
<strong>ProcessoRegistrazioneInteresseUtente</strong> ::=<br />
(<strong>reg</strong>: UT -> AS; <strong>reg_res</strong>: AS -> UT)
</p>

<p>
<strong>VerificaGiornaliera</strong> ::=<br />
(
<ul>
    <li> ( <strong>control</strong>: AS -> CA<sub><em>1</em></sub> ; <strong>control_res</strong>: CA<sub><em>1</em></sub> -> AS ) | </li>
    <li> ... |</li>
    <li> ( <strong>control</strong>: AS -> CA<sub><em>N</em></sub> ; <strong>control_res</strong>: CA<sub><em>N</em></sub> -> AS ) </li>
</ul>
) ;<br />
(
<ul>
    <li> ( <strong>notify</strong>: AS -> PG ; <strong>message</strong>: PG -> UT ) + 1</li>
</ul>
)

</p>

<p>
<strong>NotificaVoliLastMinute</strong> ::=<br />
( <strong>last_minute</strong>: CA<sub><em>i</em></sub> -> AS ) ;<br />
(  
<ul>
    <li>( <strong>notify</strong>: AS -> PG ; <strong>message</strong>: PG -> UT ) + 1</li>  
</ul>
)

</p>

<p>
<strong>AcquistoOfferta</strong> ::=  <br />
( <strong>ins_code</strong>: UT -> AS ) ;<br />  
(  
<ul>
    <li>( <strong>req_pay</strong>: AS -> PP ; <strong>pay_offer</strong>: PP -> UT ; <strong>pay_offer_res</strong>: UT -> PP ; <strong>req_pay_res</strong>: PP -> AS ) ;</li>
    <li>( </li>
    <li><ul>
    	<li>( <strong>buy_flights</strong>: AS -> CA<sub><em>i</em></sub> ; <strong>buy_flights_res</strong>: CA<sub><em>i</em></sub> -> AS ) ;</li>  
    	<li>(</li>
        <li><ul>
            <li>(</li>  
            <!-- Calcolo distanza aeroporto/casa -->
            <li><ul>
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
        <li><strong>send_tickets</strong>: AS -> UT </li>
    </ul></li>
    <li>) </li>  
    <li> + <strong>payment_failure</strong>: AS -> UT </li>
</ul>
)<br />
+ <strong>ins_code_failure</strong>: AS -> UT
</p>
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
<p>
proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, AS) =<br />
( <strong>reg</strong>@UT ; <span style="text-decoration: overline"><strong>reg_res</strong></span>@UT )
</p>

<p>
proj(</strong><strong>VerificaGiornaliera</strong>, AS) =<br /> 
(
<ul>
<li>(<span style="text-decoration: overline"><strong>control</strong></span>@CA<sub><em>1</em></sub> ; <strong>control_res</strong>@CA<sub><em>1</em></sub> ) |</li>
<li>... |</li>
<li>(<span style="text-decoration: overline"><strong>control</strong></span>@CA<sub><em>N</em></sub> ; <strong>control_res</strong>@CA<sub><em>N</em></sub> )</li>
</ul>
) ;<br />
(
<ul>
<li>( <span style="text-decoration: overline"><strong>notify</strong></span>@PG ; 1 ) + 1</li>
</ul>
)
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, AS) =<br />
( <strong>last_minute</strong>@CA<sub><em>i</em></sub> ) ;<br />
(
<ul>
<li>( <span style="text-decoration: overline"><strong>notify</strong></span>@PG ; 1 ) + 1</li>
</ul>
)
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, AS) =<br />
( <strong>ins_code</strong>@UT ) ;<br />
(<br />
<ul>
<li>( <span style="text-decoration: overline"><strong>req_pay</strong></span>@PP ; 1 ; 1 ; <strong>req_pay_res</strong>@AS ) ;</li>
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
	<li><span style="text-decoration: overline"><strong>send_tickets</strong></span>@UT</li>
</ul></li>
<li>)</li>
<li>+ <span style="text-decoration: overline"><strong>payment_failure</strong></span>@UT</li>
</ul>
)<br />
+ <span style="text-decoration: overline"><strong>ins_code_failure</strong></span>@UT
</p>
<h3>UT (UTente)</h3>

<p>
proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, UT) =<br />
( <span style="text-decoration: overline"><strong>reg</strong></span>@AS ; <strong>reg_res</strong>@AS )
</p>

<p>
proj(<strong>VerificaGiornaliera</strong>, UT) =<br />
(<br />
<ul>
<li> ( 1 ; 1 ) |</li>  
<li>... |</li> 
<li> ( 1 ; 1 )</li>  
</ul>
) ;  <br />
(<br />
<ul>
<li> ( 1 ; <strong>message</strong>@PG ) + 1</li>  
</ul>
)  
= <strong>message</strong>@PG
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, UT) =<br />
( 1 ) ;<br />
(
<ul>
<li>( 1 ; <strong>message</strong>@PG ) + 1</li>  
</ul>
)  
= <strong>message</strong>@PG
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, UT) =<br />
( <span style="text-decoration: overline"><strong>ins_code</strong></span>@AS );<br />
(
<ul>
<li>( 1 ; <strong>pay_offer</strong>@PP ; <span style="text-decoration: overline"><strong>pay_offer_res</strong></span>@PP ; 1 ) ;</li>  
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
+ <strong>ins_code_failure</strong>@AS
</p>

<h3>DG (Distanze Geografiche)</h3>

<p>
proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, DG) =<br />
( 1 ; 1 ) = 1
</p>

<p>
proj(<strong>VerificaGiornaliera</strong>, DG) =<br />
(
<ul>
<li>(1 ; 1) |</li>  
<li>... |</li>   
<li>( 1 ; 1 )</li>  
</ul>
) ;  
<ul>
<li>( 1 ; 1 ) + 1</li>  
</ul>
)  
= 1
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, DG) =<br />
( 1 ) ;<br />  
(
<ul>
<li>( 1 ; 1 ) + 1</li>  
</ul>
)  
= 1
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, DG) =<br />
( 1 ) ;<br />
(
<ul>
<li>( 1 ; 1 ; 1 ; 1 ) ;</li>  
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
+ 1
</p>

<h3>PP (Provider dei Pagamenti)</h3>

<p>
proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, PP) =<br />
( 1 ; 1 ) = 1
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
<li>( 1 ; 1 ) + 1</li>  
</ul>
) = 1
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, PP) =<br />
( 1 ) ;<br/>  
(
<ul>
<li>( 1 ; 1 ) + 1</li>  
</ul>
) = 1
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, PP) =<br />
( 1 ) ; <br/>
(
<ul>
<li>( <strong>req_pay</strong>@AS ; <span style="text-decoration: overline"><strong>pay_offer</strong></span>@UT ; <strong>pay_offer_res</strong>@UT ; <span style="text-decoration: overline"><strong>req_pay_res</strong></span>@AS ) ;</li>  
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
+ 1
</p>

<h3>PG (ProntoGram)</h3>
<p>
proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, PG) =<br />
( 1 ; 1 ) = 1
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
    <li>( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT ) + 1</li>
</ul>
) = ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>message</strong></span>@UT )  
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, PG) =<br />
( 1 ) ;<br />
(
<ul>
    <li>( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>notify</strong></span>@UT ) + 1</li>
</ul>
) = ( <strong>notify</strong>@AS ; <span style="text-decoration: overline"><strong>notify</strong></span>@UT )
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, PG) =<br />
( 1 ) ;<br />
(
<ul>
    <li>( 1 ; 1 ; 1 ; 1 ) ;</li>
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
+ 1
</p>

<h3>CT<sub><em>j</em></sub> (Compagnia Trasporti)</h3>
<p>
proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, CT<sub><em>j</em></sub>) =<br />
( 1 ; 1 ) = 1
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
    <li>( 1 ; 1 ) + 1</li>
</ul>
) = 1
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, CT<sub><em>j</em></sub>) =<br />
( 1 ) ;<br />
(
<ul>
    <li>( 1 ; 1 ) + 1</li>
</ul>
) = 1
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, CT<sub><em>j</em></sub>) =<br />
( 1 ) ;<br /> 
(
<ul>
    <li>( 1 ; 1 ; 1 ; 1 ) ;</li>
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
+ 1
</p>

<h3>CA<sub><em>i</em></sub> (Compagnia Aerea)</h3>
<p>
proj(<strong>ProcessoRegistrazioneInteresseUtente</strong>, CA<sub><em>i</em></sub>) =<br />
( 1 ; 1 ) = 1
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
    <li>( 1 ; 1 ) + 1 </li>
</ul>
) = ( <strong>control</strong>@AS ; <span style="text-decoration: overline"><strong>control_res</strong></span>@AS )
</p>

<p>
proj(<strong>NotificaVoliLastMinute</strong>, CA<sub><em>i</em></sub>) =<br />
( <span style="text-decoration: overline"><strong>last_minute</strong></span>@AS ) ;<br />
(
<ul>
    <li>( 1 ; 1 ) + 1</li>
</ul>
) = <span style="text-decoration: overline"><strong>last_minute</strong></span>@AS
</p>

<p>
proj(<strong>AcquistoOfferta</strong>, CA<sub><em>i</em></sub>) =<br />
( 1 ) ;<br />
(
<ul>
    <li>( 1 ; 1 ; 1 ; 1 ) ;</li>
    <li>(</li>
    <li><ul>
        <li>( <strong>buy_flights</strong>@AS ; <span style="text-decoration: overline"><strong>buy_flights_res</strong></span>@CA ) ;  </li>
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
+ 1
</p>
</div>
