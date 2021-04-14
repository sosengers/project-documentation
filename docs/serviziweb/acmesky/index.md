# ACMESky - API RESTful

<a name="documentation-for-api-endpoints"></a>
## Documentation for API Endpoints

Inside the Docker `acmesky-network`, all URIs are relative to *http://acmesky_backend:8080*.  
From outside Docker, the port for reaching it is *9000*.

Method | HTTP request | Description
------------- | ------------- | -------------
[**buyOffer**](Apis/DefaultApi.md#buyoffer) | **POST** /offers/buy | buyOffer
[**publishLastMinuteOffer**](Apis/DefaultApi.md#publishlastminuteoffer) | **POST** /offers/lastminute | publishLastMinuteOffer
[**registerInterest**](Apis/DefaultApi.md#registerinterest) | **POST** /interests | registerInterest
[**sendPaymentInformation**](Apis/DefaultApi.md#sendpaymentinformation) | **POST** /payments | sendPaymentInformation


<a name="documentation-for-models"></a>
## Documentation for Models

 - [Address](Models/Address.md)
 - [BuyOfferResponse](Models/BuyOfferResponse.md)
 - [Error](Models/Error.md)
 - [Flight](Models/Flight.md)
 - [Interest](Models/Interest.md)
 - [OfferPurchaseData](Models/OfferPurchaseData.md)
 - [PaymentInformation](Models/PaymentInformation.md)