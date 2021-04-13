# Payment Provider - API RESTful

<a name="documentation-for-api-endpoints"></a>
## Documentation for API Endpoints

All URIs are relative to *http://localhost:3000*

Method | HTTP request | Description
------------- | ------------- | -------------
[**createPaymentRequest**](Apis/DefaultApi.md#createpaymentrequest) | **POST** /payments/request | createPaymentRequest
[**getPaymentDetails**](Apis/DefaultApi.md#getpaymentdetails) | **GET** /payments/{transaction_id} | getPaymentDetails
[**sendPayment**](Apis/DefaultApi.md#sendpayment) | **POST** /payments/pay | sendPayment


<a name="documentation-for-models"></a>
## Documentation for Models

 - [Error](./Models/Error.md)
 - [PaymentCreationResponse](./Models/PaymentCreationResponse.md)
 - [PaymentData](./Models/PaymentData.md)
 - [PaymentRequest](./Models/PaymentRequest.md)