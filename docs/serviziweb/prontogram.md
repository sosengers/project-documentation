# ProntoGram - API RESTful

## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a *http://prontogram_backend:8080*.  
Da fuori Docker, la porta per raggiungere il servizio è *5001*.

| Method                             | Description                                                                                | API for |
|------------------------------------|--------------------------------------------------------------------------------------------|---------|
| [**POST** /messages](#sendmessage) | Permette di inviare il messaggio a ProntoGram per essere inoltrato all'utente specificato. | ACMESky |

## Richieste

<a name="sendMessage"></a>
### **POST** /messages
Permette di inviare il messaggio a ProntoGram per essere inoltrato all'utente specificato.

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
