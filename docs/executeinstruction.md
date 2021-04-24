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

- [project-source](https://github.com/sosengers/project-sources)
- [project-documentation](https://github.com/sosengers/project-documentation)

Il repository [project-source](https://github.com/sosengers/project-sources) contiene i sorgente dei diversi servizi (submodules del repository) e alcuni file di configurazione per il lancio dei servizi tramite `docker-compose`.

Per clonare il repository e i suoi submodule è sufficiente utilizzare il comando:

```bash
git clone --recurse-submodules git@github.com:sosengers/project-sources.git
```

## 2. Creare i file .env necessari
Per poter funzionare correttamente alcuni servizi hanno bisogno che alcune variabili d'ambiente siano impostate prima della loro esecuzione. Per far questo è necessario creare dei file `.env` in specifiche cartelle. Questi file contengono password, API KEY e URL che cambiano da macchina a macchina.

- `flight-company/.env`
```bash
POSTGRES_USER="flight_company_admin"
POSTGRES_PASSWORD="password"
```
- `geographical-distances/.env`
```bash
  OPEN_CAGE_API_KEY="OpenCage API key"
```
- `acmesky-middleware/.env`
```bash
FLASK_SECRET="flasksecret"
RABBITMQ_HOST="acmesky_mq"
```
- `ProntoGram-Frontend/.env`
```bash
FLASK_SECRET="flasksecret"
RABBITMQ_HOST="prontogram_mq"
```
- `camunda-workers/.env`
```bash
POSTGRES_USER="acmesky_admin"
POSTGRES_PASSWORD="password"
```
- `ProntoGram-Frontend/prontogram/static/js/environment.example.js`
```javascript
const prontogramSocket = 'http://0.0.0.0:8000';
```

## 3. Eseguire i servizi
Il file `docker-compose.yml` contiene le indicazioni per docker-compose su come debbano essere avviati i diversi servizi, 
Per eseguire tutti i servizi è sufficiente lanciare il comando:
```bash
docker-compose up --build
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

Di default i log di alcuni servzi sono disabilitati per avere un output più chiaro e pulito. Per riabilitarli è sufficiente commentare (`#`) le righe nel file `docker-compose.yml` in cui il driver per il log viene definte come `none`:
```yaml
logging:
  driver: none
```
