# DefaultApi

All URIs are relative to *http://localhost:3000*

Method | HTTP request | Description
------------- | ------------- | -------------
[**createPaymentRequest**](DefaultApi.md#createPaymentRequest) | **POST** /payments/request | createPaymentRequest
[**getPaymentDetails**](DefaultApi.md#getPaymentDetails) | **GET** /payments/{transaction_id} | getPaymentDetails
[**sendPayment**](DefaultApi.md#sendPayment) | **POST** /payments/pay | sendPayment


<a name="createPaymentRequest"></a>
# **createPaymentRequest**
> PaymentCreationResponse createPaymentRequest(PaymentRequest)

createPaymentRequest

    Creates a payment request for a user. API for: ACMESky

### Parameters

Name | Type | Description  | Notes
------------- | ------------- | ------------- | -------------
 **PaymentRequest** | [**PaymentRequest**](../Models/PaymentRequest.md)|  | [optional]

### Return type

[**PaymentCreationResponse**](../Models/PaymentCreationResponse.md)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: application/json
- **Accept**: application/json

<a name="getPaymentDetails"></a>
# **getPaymentDetails**
> PaymentRequest getPaymentDetails(transaction\_id)

getPaymentDetails

    Gets the information for the payment request for a user. API for: User

### Parameters

Name | Type | Description  | Notes
------------- | ------------- | ------------- | -------------
 **transaction\_id** | **UUID** | ID of transaction | [default to null]

### Return type

[**PaymentRequest**](../Models/PaymentRequest.md)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: Not defined
- **Accept**: application/json

<a name="sendPayment"></a>
# **sendPayment**
> sendPayment(PaymentData)

sendPayment

    Sends the payment data for paying a request. API for: User

### Parameters

Name | Type | Description  | Notes
------------- | ------------- | ------------- | -------------
 **PaymentData** | [**PaymentData**](../Models/PaymentData.md)|  | [optional]

### Return type

null (empty response body)

### Authorization

No authorization required

### HTTP request headers

- **Content-Type**: application/json
- **Accept**: application/json

