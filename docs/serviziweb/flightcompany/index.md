# Flight Company - API RESTful

<a name="documentation-for-api-endpoints"></a>
## Documentation for API Endpoints

Inside the Docker `acmesky-network`, all URIs are relative to:

- *http://flight_company_1:8080* for Flight Company 1;
- *http://flight_company_2:8080*  for Flight Company 2;
- *http://flight_company_3:8080* for Flight Company 3.


From outside Docker, the port for reaching them are respectively *7001*, *7002*, *7003*.

Method | HTTP request | Description
------------- | ------------- | -------------
[**buyFlights**](Apis/DefaultApi.md#buyflights) | **POST** /flights/buy | buyFlights
[**getFlightOffers**](Apis/DefaultApi.md#getflightoffers) | **GET** /flights/offers | getFlightOffers


<a name="documentation-for-models"></a>
## Documentation for Models

 - [Flight](Models/Flight.md)
 - [FlightToPurchase](Models/FlightToPurchase.md)
 - [Flights](Models/Flights.md)
 - [FlightsToPurchase](Models/FlightsToPurchase.md)
