# DefaultApi

All URIs are relative to *http://localhost:3000*

Method | HTTP request | Description
------------- | ------------- | -------------
[**buyOffer**](DefaultApi.md#buyOffer) | **POST** /offers/buy | buyOffer
[**publishLastMinuteOffer**](DefaultApi.md#publishLastMinuteOffer) | **POST** /offers/lastminute | publishLastMinuteOffer
[**registerInterest**](DefaultApi.md#registerInterest) | **POST** /interests | registerInterest
[**sendPaymentInformation**](DefaultApi.md#sendPaymentInformation) | **POST** /payments | sendPaymentInformation


<a name="buyOffer"></a>
# **buyOffer**
> BuyOfferResponse buyOffer(OfferPurchaseData)

buyOffer

    Requires to start the buying process of the offer with the given offer code. API for: User

### Parameters

Name | Type | Description  | Notes
------------- | ------------- | ------------- | -------------
 **OfferPurchaseData** | [**OfferPurchaseData**](../Models/OfferPurchaseData.md)|  | [optional]

### Return type

[**BuyOfferResponse**](../Models/BuyOfferResponse.md)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: application/json, application/xml
- **Accept**: application/json

<a name="publishLastMinuteOffer"></a>
# **publishLastMinuteOffer**
> publishLastMinuteOffer(company\_name, Flight)

publishLastMinuteOffer

    Allows flight companies to notify ACMESky of the presence of new last minute offers. API for: Flight Company

### Parameters

Name | Type | Description  | Notes
------------- | ------------- | ------------- | -------------
 **company\_name** | **String**| Name of the flight company | [default to null]
 **Flight** | [**List**](../Models/Flight.md)|  | [optional]

### Return type

null (empty response body)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: application/json
- **Accept**: Not defined

<a name="registerInterest"></a>
# **registerInterest**
> registerInterest(Interest)

registerInterest

    Register the user interest for roundtrip flights. API for: User

### Parameters

Name | Type | Description  | Notes
------------- | ------------- | ------------- | -------------
 **Interest** | [**Interest**](../Models/Interest.md)|  | [optional]

### Return type

null (empty response body)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: application/json
- **Accept**: application/json

<a name="sendPaymentInformation"></a>
# **sendPaymentInformation**
> sendPaymentInformation(PaymentInformation)

sendPaymentInformation

    Sends the information received by the user for verification purposes. API for: Payment Provider

### Parameters

Name | Type | Description  | Notes
------------- | ------------- | ------------- | -------------
 **PaymentInformation** | [**PaymentInformation**](../Models/PaymentInformation.md)|  | [optional]

### Return type

null (empty response body)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: application/json
- **Accept**: Not defined

