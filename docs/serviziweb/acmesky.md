Torna a [Servizi web](../serviziweb.md).

## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a *http://acmesky_backend:8080*.  
Da fuori Docker, la porta per raggiungere il servizio è *9000*.

| Risorsa | Descrizione | Risorsa per |
|---------|-------------|-------------|
| [**POST** /offers/buy](#buyOffer)                      | Richiede l'avvio di un processo di acquisto dell'offerta con il codice offerta passato come argomento. | Utente finale      |
| [**POST** /offers/lastminute](#publishLastMinuteOffer) | Permette alle compagnie aeree di notificare *ACMESky* della presenza di nuove offerte last minute.     | *Flight Company*   |
| [**POST** /interests](#registerInterest)               | Registra l'interesse di un utente per la ricezione di offerte di volo A/R.                             | Utente finale      |
| [**POST** /payments](#sendPaymentInformation)          | Invia le informazioni di pagamento ricevute dall'utente a fini di verifica.                            | *Payment Provider* |

## Richieste

<a name="buyOffer"></a>
### **POST** /offers/buy
Richiede l'avvio di un processo di acquisto dell'offerta con il codice offerta passato come argomento.

#### Parametri

| Nome                  | Tipo                                                 |
|-----------------------|------------------------------------------------------|
| **OfferPurchaseData** | [**OfferPurchaseData**](#offerpurchasedata) |

#### Tipo ritornato

- **200**: [**BuyOfferResponse**](#buyofferresponse)
- **400**: [**Error**](#error)

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

<a name="publishLastMinuteOffer"></a>
### **POST** /offers/lastminute
Permette alle compagnie aeree di notificare *ACMESky* della presenza di nuove offerte last minute.

#### Parametri

| Nome              | Tipo                         |
|-------------------|------------------------------|
| **company\_name** | **String**                   |
| **Flight**        | [**List<Flight>**](#flight) |

#### Tipo ritornato

- **200**: -
- **400**: -

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

<a name="registerInterest"></a>
### **POST** /interests
Registra l'interesse di un utente per la ricezione di offerte di volo A/R.

#### Parametri

| Nome         | Tipo                               |
|--------------|------------------------------------|
| **Interest** | [**Interest**](#interest) |

#### Tipo ritornato
- **200**: -
- **400**: [**Error**](#error)

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

<a name="sendPaymentInformation"></a>
### **POST** /payments
Invia le informazioni di pagamento ricevute dall'utente a fini di verifica.

#### Parametri

| Nome                   | Tipo                                                   |
|------------------------|--------------------------------------------------------|
| **PaymentInformation** | [**PaymentInformation**](#paymentinformation) |

#### Tipo ritornato

- **200**: -
- **400**: -

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

## Modelli

<a name="address"></a>
### Address

| Nome          | Tipo       |
|---------------|------------|
| **street**    | **String** |
| **number**    | **String** |
| **city**      | **String** |
| **zip\_code** | **String** |
| **country**   | **String** |

<a name="buyofferresponse"></a>
### BuyOfferResponse

| Nome                    | Tipo       |
|-------------------------|------------|
| **communication\_code** | **String** |

<a name="error"></a>
### Error

| Nome            | Tipo       |
|-----------------|------------|
| **Descrizione** | **String** |

<a name="flight"></a>
### Flight

| Nome                         | Tipo         |
|------------------------------|--------------|
| **flight\_id**               | **String**   |
| **departure\_airport\_code** | **String**   |
| **arrival\_airport\_code**   | **String**   |
| **cost**                     | **Double**   |
| **departure\_datetime**      | **DateTime** |
| **arrival\_datetime**        | **DateTime** |

<a name="interest"></a>
### Interest

| Nome                         | Tipo       |
|------------------------------|------------|
| **departure\_airport\_code** | **String** |
| **arrival\_airport\_code**   | **String** |
| **min\_departure\_date**     | **Date**   |
| **max\_comeback\_date**      | **Date**   |
| **max\_price**               | **Double** |
| **prontogram\_username**     | **String** |

<a name="offerpurchasedata"></a>
### OfferPurchaseData

| Nome            | Tipo                      |
|-----------------|---------------------------|
| **offer\_code** | **String**                |
| **address**     | [**Address**](#address) |
| **name**        | **String**                |
| **surname**     | **String**                |

<a name="paymentinformation"></a>
### PaymentInformation

| Nome                | Tipo         |
|---------------------|--------------|
| **transaction\_id** | **UUID**     |
| **status**          | **Boolean** |

## Interfaccia OpenAPI

Nel seguente blocco (cliccare sulla barra con su scritto "OpenAPI" in basso per aprirlo) è possibile visualizzare l'interfaccia OpenAPI che descrive il funzionamento delle API fornite da *ACMESky*.

??? openapi "OpenAPI"
    ```yaml
    --8<-- "docs/openapi/acmesky.v1.yaml"
    ```

Torna a [Servizi web](../serviziweb.md).