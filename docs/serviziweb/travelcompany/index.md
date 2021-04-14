# Travel Company - API SOAP

<a name="documentation-for-api-endpoints"></a>
## Documentation for API Endpoints

Inside the Docker `acmesky-network`, all URIs are relative to:

- *http://travel_company_1:8080* for Travel Company 1;
- *http://travel_company_2:8080*  for Travel Company 2;
- *http://travel_company_3:8080* for Travel Company 3.


From outside Docker, the port for reaching them are respectively *6001*, *6002*, *6003*.

Method | HTTP request | Description
------------- | ------------- | -------------
[**buyTransfers**](Apis/DefaultApi.md#buytransfers) | **POST** /transfers/buy | buyTransfer


<a name="documentation-for-models"></a>
## Documentation for Models

 - [Error](Models/Error.md)
 - [PurchaseDetails](Models/PurchaseDetails.md)
 - [Response](Models/Response.md)
