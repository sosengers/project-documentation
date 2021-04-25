Torna a [Servizi web](../serviziweb.md).
## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a:

- *http://flight_company_1:8080* for Flight Company 1;
- *http://flight_company_2:8080*  for Flight Company 2;
- *http://flight_company_3:8080* for Flight Company 3.


Da fuori Docker, le porte per raggiungere i servizi sono rispettivamente: *7001*, *7002*, *7003*.

| Risorsa                                     | Descrizione                                           | Risorsa per |
|---------------------------------------------|-------------------------------------------------------|-------------|
| [**POST** /flights/buy](#buyFlights)        | Acquista i voli richiesti e passati come argomento.   | *ACMESky*   |
| [**GET** /flights/offers](#getFlightOffers) | Ritorna le offerte giornaliere della compagnia aerea. | *ACMESky*   |

## Richieste

<a name="buyFlights"></a>
### **POST** /flights/buy
Acquista i voli richiesti e passati come argomento.

#### Parametri

| Nome                  | Tipo                                        |
|-----------------------|---------------------------------------------|
| **FlightsToPurchase** | [**FlightsToPurchase**](#flightstopurchase) |

#### Tipo di ritorno

- **200**: -
- **400**: -

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

<a name="getFlightOffers"></a>
### **GET** /flights/offers
Ritorna le offerte giornaliere della compagnia aerea.

#### Parametri
Questo endpoint non richiede alcun parametro.

#### Tipo di ritorno

- **200**: [**Flights**](#flights)

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

## Modelli

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

<a name="flightstopurchase"></a>
### FlightsToPurchase

| Nome                 | Tipo                                            |
|----------------------|-------------------------------------------------|
| **flight\_requests** | [**List<FlightToPurchase>**](#flighttopurchase) |

<a name="flights"></a>
### Flights

| Nome        | Tipo                        |
|-------------|-----------------------------|
| **flights** | [**List<Flight>**](#flight) |

<a name="flighttopurchase"></a>
### FlightToPurchase

| Nome           | Tipo       |
|----------------|------------|
| **flight\_id** | **String** |
| **date**       | **Date**   |

## Interfaccia OpenAPI

Nel seguente blocco (cliccare sulla barra con su scritto "OpenAPI" in basso per aprirlo) è possibile visualizzare l'interfaccia OpenAPI che descrive il funzionamento delle API fornite da Flight Company.

??? openapi "OpenAPI"
    ```yaml
    --8<-- "docs/openapi/flightcompany.v1.yaml"
    ```

Torna a [Servizi web](../serviziweb.md).
