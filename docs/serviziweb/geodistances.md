# Geographical Distances - API RESTful

## Panoramica

Dentro la rete Docker `acmesky-network`, tutti gli URI sono relativi a *http://geographical_distances:8080*.  
Da fuori Docker, la porta per raggiungere il servizio è *5000*.

| Risorsa | Descrizione | Risorsa per |
|---------|-------------|-------------|
| [**POST** /distance](#calculatedistance) | Calcola la distanza fra i dati indirizzi. Gli indirizzi vengono prima trasformati in coordinate GPS tramite *geocoding* (è richiesto che gli indirizzi siano il più possibile precisi, altrimenti il sistema trova le coordinate relative a un luogo il cui indirizzo meglio assomiglia a quello passato come argomento), successivamente la distanza viene calcolata mediante la [formula di Haversine [EN]](https://en.wikipedia.org/wiki/Haversine_formula) e ritornata in kilometri. | ACMESky |

## Richieste

<a name="calculateDistance"></a>
### **POST** /distance
Calcola la distanza fra i dati indirizzi. Gli indirizzi vengono prima trasformati in coordinate GPS tramite *geocoding* (è richiesto che gli indirizzi siano il più possibile precisi, altrimenti il sistema trova le coordinate relative a un luogo il cui indirizzo meglio assomiglia a quello passato come argomento), successivamente la distanza viene calcolata mediante la [formula di Haversine [EN]](https://en.wikipedia.org/wiki/Haversine_formula) e ritornata in kilometri.

#### Parametri

| Nome          | Tipo                        |
|---------------|-----------------------------|
| **Locations** | [**Locations**](#locations) |

#### Tipo di ritorno

- **200**: **Double**
- **400**: -

#### Header della richiesta

- **Content-Type**: application/json
- **Accept**: application/json

## Modelli

<a name="locations"></a>
### Locations

| Nome           | Tipo       |
|----------------|------------|
| **address\_1** | **String** |
| **address\_2** | **String** |