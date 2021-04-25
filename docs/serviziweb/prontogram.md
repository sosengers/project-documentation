Torna a [Servizi web](../serviziweb.md).
## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a *http://prontogram_backend:8080*.  
Da fuori Docker, la porta per raggiungere il servizio è *5001*.

| Risorsa | Descrizione | Risorsa per |
|---------|-------------|-------------|
| [**POST** /messages](#sendMessage) | Permette di inviare il messaggio a ProntoGram per essere inoltrato all'utente specificato. | *ACMESky* |

## Richieste

<a name="sendMessage"></a>
### **POST** /messages
Permette di inviare il messaggio a *ProntoGram* per essere inoltrato all'utente specificato.

#### Parametri

| Nome        | Tipo                             |
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

| Nome           | Tipo         |
|----------------|--------------|
| **sender**     | **String**   |
| **receiver**   | **String**   |
| **body**       | **String**   |
| **send\_time** | **DateTime** |

## Interfaccia OpenAPI

Nel seguente blocco (cliccare sulla barra con su scritto "OpenAPI" in basso per aprirlo) è possibile visualizzare l'interfaccia OpenAPI che descrive il funzionamento delle API fornite da ProntoGram.

??? openapi "OpenAPI"
    ```yaml
    --8<-- "docs/openapi/prontogram.v1.yaml"
    ```

Torna a [Servizi web](../serviziweb.md).