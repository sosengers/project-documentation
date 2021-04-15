# ACMESky - API RESTful

## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a *http://acmesky_backend:8080*.  
Da fuori Docker, la porta per raggiungere il servizio è *9000*.

| Method                                                 | Description                                                                                            | API for          |
|--------------------------------------------------------|--------------------------------------------------------------------------------------------------------|------------------|
| [**POST** /offers/buy](#buyoffer)                      | Richiede l'avvio di un processo di acquisto dell'offerta con il codice offerta passato come argomento. | User             |
| [**POST** /offers/lastminute](#publishlastminuteoffer) | Permette alle compagnie aeree di notificare ACMESky della presenza di nuove offerte last minute.       | Flight Company   |
| [**POST** /interests](#registerinterest)               | Registra l'interesse di un utente per la ricezione di offerte di volo A/R.                             | User             |
| [**POST** /payments](#sendpaymentinformation)          | Invia le informazioni di pagamento ricevute dall'utente a fini di verifica.                            | Payment Provider |

## Richieste

<a name="buyOffer"></a>
### **POST** /offers/buy
Requires to start the buying process of the offer with the given offer code.

#### Parametri

| Name                  | Type                                                 |
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
Allows flight companies to notify ACMESky of the presence of new last minute offers.

#### Parametri

| Name              | Type                         |
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
Register the user interest for roundtrip flights.

#### Parametri

| Name         | Type                               |
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
Sends the information received by the user for verification purposes.

#### Parametri

| Name                   | Type                                                   |
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

| Name          | Type       |
|---------------|------------|
| **street**    | **String** |
| **number**    | **String** |
| **city**      | **String** |
| **zip\_code** | **String** |
| **country**   | **String** |

<a name="buyofferresponse"></a>
### BuyOfferResponse

| Name                    | Type       |
|-------------------------|------------|
| **communication\_code** | **String** |

<a name="error"></a>
### Error

| Name            | Type       |
|-----------------|------------|
| **description** | **String** |

<a name="flight"></a>
### Flight

| Name                         | Type         |
|------------------------------|--------------|
| **flight\_id**               | **String**   |
| **departure\_airport\_code** | **String**   |
| **arrival\_airport\_code**   | **String**   |
| **cost**                     | **Double**   |
| **departure\_datetime**      | **DateTime** |
| **arrival\_datetime**        | **DateTime** |

<a name="interest"></a>
### Interest

| Name                         | Type       |
|------------------------------|------------|
| **departure\_airport\_code** | **String** |
| **arrival\_airport\_code**   | **String** |
| **min\_departure\_date**     | **Date**   |
| **max\_comeback\_date**      | **Date**   |
| **max\_price**               | **Double** |
| **prontogram\_username**     | **String** |

<a name="offerpurchasedata"></a>
### OfferPurchaseData

| Name            | Type                      |
|-----------------|---------------------------|
| **offer\_code** | **String**                |
| **address**     | [**Address**](#address) |
| **name**        | **String**                |
| **surname**     | **String**                |

<a name="paymentinformation"></a>
### PaymentInformation

| Name                | Type         |
|---------------------|--------------|
| **transaction\_id** | **UUID**     |
| **status**          | **Boolean**) |
