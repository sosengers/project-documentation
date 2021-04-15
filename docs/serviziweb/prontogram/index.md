# ProntoGram - API RESTful

## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a *http://prontogram_backend:8080*.  
Da fuori Docker, la porta per raggiungere il servizio è *5001*.

| Method                             | Description                                                              | API for |
|------------------------------------|--------------------------------------------------------------------------|---------|
| [**POST** /messages](#sendmessage) | Sends the message to ProntoGram for being dispatched to the actual user. | ACMESky |

## Richieste

<a name="sendMessage"></a>
### **POST** /messages
Sends the message to ProntoGram for being dispatched to the actual user.

#### Parametri

| Name        | Type                             |
|-------------|----------------------------------|
| **Message** | [**Message**](#message) |

#### Tipo di ritorno

- **200**: -

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json


## Modelli

<a name="message"></a>
### Message

| Name           | Type         |
|----------------|--------------|
| **sender**     | **String**   |
| **receiver**   | **String**   |
| **body**       | **String**   |
| **send\_time** | **DateTime** |
