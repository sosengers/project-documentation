# Flight Company - API RESTful

## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a:

- *http://flight_company_1:8080* for Flight Company 1;
- *http://flight_company_2:8080*  for Flight Company 2;
- *http://flight_company_3:8080* for Flight Company 3.


Da fuori Docker, le porte per raggiungere i servizi sono respectively *7001*, *7002*, *7003*.

| Method                                      | Description                                           | API for |
|---------------------------------------------|-------------------------------------------------------|---------|
| [**POST** /flights/buy](#buyflights)        | Acquista i voli richiesti e passati come argomento.   | ACMESky |
| [**GET** /flights/offers](#getflightoffers) | Ritorna le offerte giornaliere della compagnia aerea. | ACMESky |

## Richieste

<a name="buyFlights"></a>
### **POST** /flights/buy
Buys the flights with the given purchase details.

#### Parametri

| Name                  | Type                                                 |
|-----------------------|------------------------------------------------------|
| **FlightsToPurchase** | [**FlightsToPurchase**](#flightstopurchase) |

#### Tipo di ritorno

- **200**: -
- **400**: -

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

<a name="getFlightOffers"></a>
### **GET** /flights/offers
Returns the daily flight offers of the company.

#### Parametri
This endpoint does not need any parameter.

#### Tipo di ritorno

- **200**: [**Flights**](#flights)

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

## Modelli

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

<a name="flightstopurchase"></a>
### FlightsToPurchase

| Name                 | Type                                              |
|----------------------|---------------------------------------------------|
| **flight\_requests** | [**List<FlightToPurchase>**](#flighttopurchase) |

<a name="flights"></a>
### Flights

| Name        | Type                          |
|-------------|-------------------------------|
| **flights** | [**List<Flight>**](#flight) |

<a name="flighttopurchase"></a>
### FlightToPurchase

| Name           | Type       |
|----------------|------------|
| **flight\_id** | **String** |
| **date**       | **Date**   |
