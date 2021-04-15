# Payment Provider - API RESTful

## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a *http://payment_provider_backend:8080*.  
Da fuori Docker, la porta per raggiungere il servizio è *4001*.

| Method                                                   | Description                                                                      | API for |
|----------------------------------------------------------|----------------------------------------------------------------------------------|---------|
| [**POST** /payments/request](#createpaymentrequest)      | Crea una richiesta di pagamento per un utente.                                   | ACMESky |
| [**GET** /payments/{transaction_id}](#getpaymentdetails) | Ritorna le informazioni relative alla richiesta di pagamento da parte di utente. | User    |
| [**POST** /payments/pay](#sendpayment)                   | Permette l'invio delle informazioni di pagamento per pagare un offerta.          | User    |

## Richieste

<a name="createPaymentRequest"></a>
### **POST** /payments/request
Crea una richiesta di pagamento per un utente. 

#### Parametri

| Name               | Type                                           |
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

| Name                | Type     |
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

| Name            | Type                                     |
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

| Name            | Type       |
|-----------------|------------|
| **description** | **String** |

<a name="paymentcreationresponse"></a>
### PaymentCreationResponse

| Name                | Type       |
|---------------------|------------|
| **redirect\_page**  | **String** |
| **transaction\_id** | **UUID**   |

<a name="paymentdata"></a>
### PaymentData

| Name                     | Type       |
|--------------------------|------------|
| **transaction\_id**      | **UUID**   |
| **credit\_cart\_number** | **String** |
| **cvv**                  | **String** |
| **expiration\_date**     | **Date**   |
| **owner\_name**          | **String** |

<a name="paymentrequest"></a>
### PaymentRequest

| Name                  | Type       |
|-----------------------|------------|
| **amount**            | **Double** |
| **description**       | **String** |
| **payment\_receiver** | **String** |