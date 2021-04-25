Torna a [Servizi web](../serviziweb.md).
## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a *http://payment_provider_backend:8080*.  
Da fuori Docker, la porta per raggiungere il servizio è *4001*.

| Risorsa | Descrizione | Risorsa per |
|---------|-------------|-------------|
| [**POST** /payments/request](#createPaymentRequest) | Crea una richiesta di pagamento per un utente. | *ACMESky* |
| [**GET** /payments/{transaction_id}](#getPaymentDetails) | Ritorna le informazioni relative alla richiesta di pagamento da parte di utente. | Utente finale |
| [**POST** /payments/pay](#sendPayment) | Permette l'invio delle informazioni di pagamento per pagare un offerta. | Utente finale |

## Richieste

<a name="createPaymentRequest"></a>
### **POST** /payments/request
Crea una richiesta di pagamento per un utente. 

#### Parametri

| Nome               | Tipo                                           |
|--------------------|------------------------------------------------|
| **PaymentRequest** | [**PaymentRequest**](#paymentrequest) |

#### Tipo di ritorno

- **200**: [**PaymentCreationResponse**](#paymentcreationresponse)
- **400**: -

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

<a name="getPaymentDetails"></a>
### **GET** /payments/{transaction_id}
Ritorna le informazioni relative alla richiesta di pagamento da parte di utente.

#### Parametri

| Nome                | Tipo     |
|---------------------|----------|
| **transaction\_id** | **UUID** |

#### Tipo di ritorno

- **200**: [**PaymentRequest**](#paymentrequest)
- **404**: -

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

<a name="sendPayment"></a>
### **POST** /payments/pay
Permette l'invio delle informazioni di pagamento per pagare un offerta.

#### Parametri

| Nome            | Tipo                                     |
|-----------------|------------------------------------------|
| **PaymentData** | [**PaymentData**](#paymentdata) |

#### Tipo di ritorno

- **200**: -
- **400**: [**Error**](#error)

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json


## Modelli

<a name="error"></a>
### Error

| Nome            | Tipo       |
|-----------------|------------|
| **description** | **String** |

<a name="paymentcreationresponse"></a>
### PaymentCreationResponse

| Nome                | Tipo       |
|---------------------|------------|
| **redirect\_page**  | **String** |
| **transaction\_id** | **UUID**   |

<a name="paymentdata"></a>
### PaymentData

| Nome                     | Tipo       |
|--------------------------|------------|
| **transaction\_id**      | **UUID**   |
| **credit\_cart\_number** | **String** |
| **cvv**                  | **String** |
| **expiration\_date**     | **Date**   |
| **owner\_name**          | **String** |

<a name="paymentrequest"></a>
### PaymentRequest

| Nome                  | Tipo       |
|-----------------------|------------|
| **amount**            | **Double** |
| **description**       | **String** |
| **payment\_receiver** | **String** |

## Interfaccia OpenAPI

Nel seguente blocco (cliccare sulla barra con su scritto "OpenAPI" in basso per aprirlo) è possibile visualizzare l'interfaccia OpenAPI che descrive il funzionamento delle API fornite da Payment Provider.

??? openapi "OpenAPI"
    ```yaml
    --8<-- "docs/openapi/paymentprovider.v1.yaml"
    ```

Torna a [Servizi web](../serviziweb.md).