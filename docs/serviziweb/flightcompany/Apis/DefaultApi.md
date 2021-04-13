# DefaultApi

All URIs are relative to *http://localhost:3000*

Method | HTTP request | Description
------------- | ------------- | -------------
[**buyFlights**](DefaultApi.md#buyFlights) | **POST** /flights/buy | buyFlights
[**getFlightOffers**](DefaultApi.md#getFlightOffers) | **GET** /flights/offers | getFlightOffers


<a name="buyFlights"></a>
# **buyFlights**
> buyFlights(FlightsToPurchase)

buyFlights

    Buys the flights with the given purchase details. API for: ACMESky

### Parameters

Name | Type | Description  | Notes
------------- | ------------- | ------------- | -------------
 **FlightsToPurchase** | [**FlightsToPurchase**](../Models/FlightsToPurchase.md)|  | [optional]

### Return type

null (empty response body)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: application/json
- **Accept**: Not defined

<a name="getFlightOffers"></a>
# **getFlightOffers**
> Flights getFlightOffers()

getFlightOffers

    Returns the daily flight offers of the company. API for: ACMESky

### Parameters
This endpoint does not need any parameter.

### Return type

[**Flights**](../Models/Flights.md)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: Not defined
- **Accept**: application/json

