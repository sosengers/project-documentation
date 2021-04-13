# DefaultApi

All URIs are relative to *http://localhost:3000*

Method | HTTP request | Description
------------- | ------------- | -------------
[**calculateDistance**](DefaultApi.md#calculateDistance) | **POST** /distance | calculateDistance


<a name="calculateDistance"></a>
# **calculateDistance**
> Double calculateDistance(Locations)

calculateDistance

    Calculates the distance between the given locations. Given addresses are first transformed into coordinates via geocoding, then the distance is computed and returned in kilometers. API for: ACMESky

### Parameters

Name | Type | Description  | Notes
------------- | ------------- | ------------- | -------------
 **Locations** | [**Locations**](../Models/Locations.md)|  | [optional]

### Return type

[**Double**](../Models/double.md)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: application/json
- **Accept**: application/json

