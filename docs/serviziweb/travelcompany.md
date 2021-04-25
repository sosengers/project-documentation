Torna a [Servizi web](../serviziweb.md).
## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a:

- *http://travel_company_1:8080* for Travel Company 1;
- *http://travel_company_2:8080* for Travel Company 2;
- *http://travel_company_3:8080* for Travel Company 3.


Da fuori Docker, le porte per raggiungere i servizi sono rispettivamente *6001*, *6002*, *6003*.

| Risorsa | Descrizione | Risorsa per |
|---------|-------------|-------------|
| [buyTransfers](#buyTransfers) | Permette di acquistare il trasferimento verso l'aeroporto passando come argomento i dettagli di acquisto. | *ACMESky* |

<a name="buyTransfers"></a>
### buyTransfers
Permette di acquistare il trasferimento verso l'aeroporto passando come argomento i dettagli di acquisto.

#### Parametri

| Nome                | Tipo                                    |
|---------------------|-----------------------------------------|
| **PurchaseDetails** | [**PurchaseDetails**](#purchasedetails) |

#### Tipi di ritorno

- **200**: [**Response**](#response)
- **500**: [**Error**](#error)

#### Header della richiesta

- **Content-Type**: application/xml
- **Accept**: application/xml


## Modelli

<a name="error"></a>
### Error

| Nome            | Tipo       |
|-----------------|------------|
| **description** | **String** |

<a name="purchasedetails"></a>
### PurchaseDetails

| Nome                              | Tipo         |
|-----------------------------------|--------------|
| **customer\_address**             | **String**   |
| **airport\_code**                 | **String**   |
| **departure\_transfer\_datetime** | **DateTime** |
| **arrival\_transfer\_datetime**   | **DateTime** |
| **customer\_name**                | **String**   |

<a name="response"></a>
### Response

| Nome         | Tipo       |
|--------------|------------|
| **response** | **String** |

## Interfaccia OpenAPI

Nel seguente blocco (cliccare sulla barra con su scritto "OpenAPI" in basso per aprirlo) è possibile visualizzare l'interfaccia OpenAPI che descrive il funzionamento delle API fornite da Travel Company.

??? openapi "OpenAPI"
    ```yaml
    --8<-- "docs/openapi/travelcompany.v1.yaml"
    ```

Torna a [Servizi web](../serviziweb.md).