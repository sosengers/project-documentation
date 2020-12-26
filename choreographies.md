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
- **control**: controllo giornaliero della presenza di voli di interesse per l'utente;
- **control_res**: risposta al task **control**;
- **notify**: notifica della presenza di voli di interesse per l'utente tramite ProntoGram (possono esserci come no, se non ci sono l'utente non viene contattato). Il codice offerta inviato è univoco per offerta: se ci sono più utenti con gli stessi interessi viene inviato lo stesso codice offerta;
- **last_minute**: compagnia aerea notifica ACMESky di un'offerta last minute;
- **ins_code**: utente inserisce il codice ricevuto via ProntoGram sul portale;
- **ins_code_res**: risposta al task **ins_code**;
- **req_pay**: ACMESky richiede il pagamento al Provider dei Pagamenti;
- **req_pay_res**: il Provider dei Pagamenti comunica ad ACMESky l'esito del pagamento;
- **calc_dist**: ACMESky richiede il calcolo della distanza al servizio esterno per il calcolo delle distanze geografiche;
- **calc_dist_res**: risposta al task **calc_dist**;
- **pren_trs**: in base alla distanza ACMESky può, oppure no, prenotare il trasporto;
- **pren_trs_res**: risposta al task **pren_trs**;
- **end_operation**: ACMESky avvisa l'utente riguardo all'esito dell'operazione (successo/fallimento). In caso di successo, inoltra il biglietto;
- **message**: ProntoGram riferisce il messaggio all'Utente (essendo un servizio esterno questa è una semplificazione);
- **pay_offer**: il Provider dei Pagamenti, ricevuta la richiesta da ACMESky, inoltra all'utente la richiesta di pagamento, il quale dovrà soddisfarla per procedere;
- **pay_offer_res**: l'utente ricevuta la richiesta da parte del Provider dei Pagamenti, paga l'offerta;
- **buy_flights**: ACMESky compra per conto dell'utente il biglietto per il volo A/R da lui/lei scelto presso la compagnia aerea CA<sub>*i*</sub> che fornisce i due voli che soddisfano il bisogno;
- **buy_flights_res**: CA<sub>*i*</sub> conferma ad ACMESky la disponibilità del volo e inoltra il biglietto.

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
( **ins_code**: UT -> AS; **ins_code_res**: AS -> UT ) ;  
(  
- (  
    - ( **req_pay**: AS -> PP ; **pay_offer**: PP -> UT ;  **pay_offer_res**: UT -> PP ; **req_pay_res**: PP -> AS ) ;  
	- (  
		- (  
			- ( **buy_flights**: AS -> CA<sub>*i*</sub> ; **buy_flights_res**: CA<sub>*i*</sub> -> AS ) ;  
			- ( **calc_dist**: AS -> DG ; **calc_dist_res**: DG -> AS ) ;  
			- (  
				- ( **pren_trs**: AS -> CT<sub>*j*</sub> ; **pren_trs_res**: CT<sub>*j*</sub> -> AS) + 1  
				
			- )  
			
		- )  
		- \+ 1  
		
	- )  
	
- )  
- \+ 1  

) ;  
( **end_operation**: AS -> UT )

</div>

### Verifica della connectedness delle coreografie
- **ProcessoRegistrazioneInteresseUtente** è connessa in quanto il ricevente in **reg** è uguale al mittente in **reg_res**.
- **VerificaGiornaliera**: la singola comunicazione fra AS e un qualsiasi CA<sub>*i*</sub> è connessa come per la precedente coreografia, in quanto il ricevente in **control** è uguale al mittente in **control_res**. Le interazioni parallele non hanno condizioni per poter verificare la connectedness e quindi non vengono verificate. Per quanto riguarda la composizione sequenziale fra la prima parte della coreografia, in cui avvengono le interazioni (**control** ; **control_res**) fra AS e i vari CA<sub>*i*</sub>, e la seconda parte, in cui avviene l'interazione **notify** fra AS e PG, è nuovamente assicurata la connectedness. Questo poiché ogni interazione all'interno della composizione parallela termina con ricevente AS e il mittente di **notify** è AS. Dopo **notify** nella sequenza c'è **message** che invia il messaggio ad UT. Essendo AS il ricevente di **notify** e il mittente di **message**, la sequenza è connessa. Nel caso in cui non avvenga **notify** fra AS e PG poiché non vi sono voli per l'utente da comunicare via ProntoGram, allora viene eseguito il ramo "1" (ovvero non viene fatto nulla). In questo caso, PG rimane in attesa di **notify** ma è corretto poiché è il suo compito.
- **NotificaVoliLastMinute** è connessa in modo non dissimile a quanto avviene in **VerificaGiornaliera**. AS è ricevente in **last_minute** e mittente in **notify** dopodiché PG è ricevente in **notify** e mittente in **message**. Da notare che **notify** e **message**, avvengono solamente se ci sono voli di interesse per l'utente e quindi nuovamente, in caso non ce ne siano, ProntoGram resterebbe in attesa ma non sarebbe un problema poiché è il suo compito.
- **AcquistoOfferta** è connessa perché:
  - il ricevente di **ins_code** corrisponde al mittente di **ins_code_res**;
  - il mittente di **req_pay** è lo stesso di **ins_code_res** e quindi si verifica la condizione di tipo "Sender" poiché non è richiesto che la ricezione da parte di UT in **ins_code_res** avvenga prima di quella in **req_pay** ma è certamente necessario che **req_pay** avvenga dopo **ins_code_res**;
  - l'interazione **req_pay** ha il ricevente corrispondente al mittente di **pay_offer**;
  - il ricevente di **pay_offer** è il mittente di **pay_offer_res**;
  - il ricevente di **pay_offer_res** è il mittente di **req_pay_res**;
  - il ricevente di **req_pay_res** è il mittente di **buy_flights**;
  - il ricevente di **buy_flights** è il mittente di **buy_flights_res**;
  - il ricevente di **buy_flights_res** è il mittente di **calc_dist**;
  - il ricevente di **calc_dist** è il mittente di **calc_dist_res**;
  - il ricevente di **calc_dist_res** è il mittente di **pren_trs**;
  - il ricevente di **pren_trs** è il mittente di **pren_trs_res**;
  - nel caso in cui le interazioni **pren_trs** e **pren_trs_res** siano avvenute allora il ricevente di **pren_trs_res** è il mittente di **end_operation** altrimenti il ricevente di **calc_dist_res** è il mittente di **end_operation**.

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
( **ins_code**@UT ; <span style="text-decoration: overline">**ins_code_res**</span>@UT ) ;  
(  
- (  
	- ( <span style="text-decoration: overline">**req_pay**</span>@PP ; 1 ; 1 ; **req_pay_res**@AS ) ;  
	- (  
		- (  
			- ( <span style="text-decoration: overline">**buy_flights**</span>@CA<sub>*i*</sub> ; **buy_flights_res**@CA<sub>*i*</sub> ) ;  
			- ( <span style="text-decoration: overline">**calc_dist**</span>@DG ; **calc_dist_res**@DG ) ;  
			- (  
				- ( <span style="text-decoration: overline">**pren_trs**</span>@CT<sub>*j*</sub> ; **pren_trs_res**@CT<sub>*j*</sub> ) + 1  
				     
			- )  
		- )  
		- \+ 1  
		
	- )  
	
- )  
- \+ 1  

) ;  
(<span style="text-decoration: overline">**end_operation**</span>@UT)  


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
( <span style="text-decoration: overline">**ins_code**</span>@AS ; **ins_code_res**@AS ) ;  
(  
- (  
	- ( 1 ; **pay_offer**@PP ; <span style="text-decoration: overline">**pay_offer_res**</span>@PP ; 1 ) ;  
	- (  
		- (  
			- ( 1 ; 1 ) ;  
			- ( 1 ; 1 ) ;  
			- (  
				- ( 1 ; 1 ) + 1  
				
			- )  
			
		- )  
		- \+ 1  
		
	- )  
	
- )  
- \+ 1  

) ;  
( **end_operation**@AS ) = ( ( <span style="text-decoration: overline">**ins_code**</span>@AS ; **ins_code_res**@AS ) ; ( **pay_offer**@PP ; <span style="text-decoration: overline">**pay_offer_res**</span>@PP ) ; **end_operation**@AS )

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
( 1 ; 1 ) ;  
(  
- (  
	- ( 1 ; 1 ; 1 ; 1 ) ;  
	- (  
		- (  
			- ( 1 ; 1 ) ;  
			- ( **calc_dist**@AS ;<span style="text-decoration: overline">**calc_dist_res**</span>@AS ) ;  
			- (  
				- ( 1 ; 1 ) + 1  
				
			- )  
			
		- )  
		- \+ 1  
		
	- )  
	
- )   
- \+ 1  
  
) ;  
( 1 ) = ( **calc_dist**@AS ; <span style="text-decoration: overline">**calc_dist_res**</span>@AS )

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
( 1 ; 1 ) ;  
(  
- (  
	- ( **req_pay**@AS ; <span style="text-decoration: overline">**pay_offer**</span>@UT ; **pay_offer_res**@UT ; <span style="text-decoration: overline">**req_pay_res**</span>@AS ) ;  
	- (  
		- (  
			- ( 1 ; 1 ) ;  
			- ( 1 ; 1 ) ;  
			- (  
			    - ( 1 ; 1 ) + 1  
		    - )  
                
		- )  
		- \+ 1  
		
	- )  
	
- )  
- \+ 1  

);  
( 1 ) = ( **req_pay**@AS ; <span style="text-decoration: overline">**pay_offer**</span>@UT ; **pay_offer_res**@UT ; <span style="text-decoration: overline">**req_pay_res**</span>@AS )  

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
( 1 ; 1 ) ;  
(  
- (  
	- ( 1 ; 1 ; 1 ; 1 ) ;  
	- (  
		- (  
			- ( 1 ; 1 ) ;  
			- ( 1 ; 1 ) ;  
			- (   
			    - ( 1 ; 1 ) + 1  
			    
		    - )  
		    
		- )  
		- \+ 1  
		
	- )  
	
- )  
- \+ 1  

);  
( 1 ) = 1

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
( 1 ; 1 ) ;  
( 
- (  
	- ( 1 ; 1 ; 1 ; 1 ) ;  
	- (  
		- (  
		    - ( 1 ; 1 ) ;  
			- ( 1 ; 1 ) ;  
			- (  
			    - ( **pren_trs**@AS ; <span style="text-decoration: overline">**pren_trs_res**</span>@AS ) + 1  
			    
			- )  
			
		- )  
		- \+ 1  
		
	- )  
	
- )  
- \+ 1  
 
) ;  
( 1 ) = ( **pren_trs**@AS ; <span style="text-decoration: overline">**pren_trs_res**</span>@AS )

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
( 1 ; 1 ) ;  
(  
- (  
	- ( 1 ; 1 ; 1 ; 1 ) ;  
	- (  
		- (  
			- ( **buy_flights**@AS ; <span style="text-decoration: overline">**buy_flights_res**</span>@AS ) ;  
			- ( 1 ; 1 ) ;  
			- (  
				- ( 1 ; 1 ) + 1  
				
			- )  
			
		- )  
		- \+ 1  
		
	- )  
	 
- )  
- \+ 1  
 
) ;  
( 1 ) = ( **buy_flights**@AS ; <span style="text-decoration: overline">**buy_flights_res**</span>@AS )

</div>