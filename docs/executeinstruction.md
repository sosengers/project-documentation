---
hide:
  - navigation
---
Il progetto è stato testato su due macchine:

- MacBook Pro 2019, Intel Core i9 2,3GHz, 16GB RAM;
- Computer di Tommaso

Entrambi con Docker versione 20.10.5, build 55c4c88 e docker-compose versione 1.29.0, build 07737305

## 1. Clonare il repository
Il progetto è diviso in due macro-repository:

- [project-sources](https://github.com/sosengers/project-sources)
- [project-documentation](https://github.com/sosengers/project-documentation)

Il repository [project-sources](https://github.com/sosengers/project-sources) contiene il codice sorgente dei diversi servizi (submodules del repository) e alcuni file di configurazione per il lancio dei servizi tramite `docker-compose`.

Per clonare il repository e i suoi submodule è sufficiente utilizzare il comando:

```bash
git clone --recurse-submodules git@github.com:sosengers/project-sources.git
```

## 2. Creare i file di environment necessari
Per poter funzionare correttamente, alcuni servizi hanno bisogno che alcune variabili d'ambiente siano impostate prima della loro esecuzione. Per far questo è necessario creare dei file di *environment* in specifiche cartelle. Questi file contengono password, API KEY e URL che cambiano da macchina a macchina.
Di seguito vengono presentati i contenuti dei file environment sufficienti per far funzionare il sistema nel suo intero:

- `flight-company/.env`
```bash
POSTGRES_USER="flight_company_admin"
POSTGRES_PASSWORD="password"
```
- `geographical-distances/.env`
```bash
OPEN_CAGE_API_KEY="OpenCage API key"
```
Al posto di `OpenCage API key` bisogna mettere una chiave di OpenCage valida, generabile creando un profilo tramite il sito [OpenCage](https://opencagedata.com/) e accedendo alla propria area utente.

- `acmesky-middleware/.env`
```bash
FLASK_SECRET="flasksecret"
RABBITMQ_HOST="acmesky_mq"
MIDDLEWARE_HOST="0.0.0.0"
MIDDLEWARE_PORT="8080"
```
- `ProntoGram-Frontend/.env`
```bash
FLASK_SECRET="flasksecret"
RABBITMQ_HOST="prontogram_mq"
PRONTOGRAM_HOST="0.0.0.0"
PRONTOGRAM_PORT="8080"
```
- `camunda-workers/.env`
```bash
POSTGRES_USER="acmesky_admin"
POSTGRES_PASSWORD="password"
MONGO_USER="root"
MONGO_PASSWORD="password"
```
- `ProntoGram-Frontend/prontogram/static/js/environment.js`
```javascript
const prontogramSocket = 'http://0.0.0.0:8000';
```
- `PaymentProvider-Backend/.env`
```bash
PAYMENT_PROVIDER_FRONTEND="http://localhost:4002"
```
- `PaymentProvider-Frontend/src/environments/environment.ts`
```typescript
export const environment = {
  paymentProviderBackend: "http://0.0.0.0:4001",
  production: true
};
```
- `ACMESky-Frontend/src/environments/environment.ts`
```typescript
export const environment = {
  acmeskyBackend: "http://0.0.0.0:9000",
  acmeskyMiddleware: "http://0.0.0.0:9001",
  production: true
};
```

## 3. Eseguire i servizi
Il file `docker-compose.yaml` contiene le indicazioni per docker-compose su come debbano essere avviati i diversi servizi.
Per eseguire tutti i servizi è sufficiente lanciare il comando:
```bash
docker-compose up
```
È possibile aggiungere gli argomenti:

- `--build` per forzare la compilazione (nel caso non sia la prima volta in cui si utilizza il comando `docker-compose` con questo file;
- `-d` per lanciare i container in modalità *detached*, liberando il terminale una volta lanciati tutti quanti.

Per spegnere tutti i servizi:
- se si è usato solo il comando `docker-compose up`, è sufficiente premere <kbd>CTRL</kbd> + <kbd>C</kbd>;
- se si è avviato in modalità *detached* è possibile invocare il comando:
```bash
docker-compose down
```

Nel caso invece si vogliano eseguire i servizi solamente di alcuni ruoli, è stato creato un Makefile che ne semplifica la selezione 

- ACMESky
```bash
make acmesky
```
- Payment Provider
```bash 
make payment_provider
```
- Travel Companies 
```bash
make travel_companies
```
- Flight Companies 
```bash
make flight_companies
```
- Geographical Distances 
```bash
make geographical_distances
```
- ProntoGram 
```bash
make prontogram
```

Di default i log di alcuni servzi sono disabilitati per avere un output più chiaro e pulito. Per riabilitarli è sufficiente commentare (`#`) le righe nel file `docker-compose.yaml` in cui il driver per il log viene definte come `none`:
```yaml
logging:
  driver: none
```
