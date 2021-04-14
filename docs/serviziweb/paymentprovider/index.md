# Payment Provider - API RESTful

<a name="documentation-for-api-endpoints"></a>
## Documentation for API Endpoints

Inside the Docker `acmesky-network`, all URIs are relative to *http://payment_provider_backend:8080*.  
From outside Docker, the port for reaching it is *4001*.

Method | HTTP request | Description
------------- | ------------- | -------------
[**createPaymentRequest**](Apis/DefaultApi.md#createpaymentrequest) | **POST** /payments/request | createPaymentRequest
[**getPaymentDetails**](Apis/DefaultApi.md#getpaymentdetails) | **GET** /payments/{transaction_id} | getPaymentDetails
[**sendPayment**](Apis/DefaultApi.md#sendpayment) | **POST** /payments/pay | sendPayment


<a name="documentation-for-models"></a>
## Documentation for Models

 - [Error](Models/Error.md)
 - [PaymentCreationResponse](Models/PaymentCreationResponse.md)
 - [PaymentData](Models/PaymentData.md)
 - [PaymentRequest](Models/PaymentRequest.md)