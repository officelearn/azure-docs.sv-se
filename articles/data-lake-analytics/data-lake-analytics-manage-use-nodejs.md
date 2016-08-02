<properties
   pageTitle="Hantera Azure Data Lake Analytics med hjälp av Azure SDK för Node.js | Azure"
   description="Lär dig att hantera Data Lake Analytics-konton, -datakällor, -jobb och -användare med hjälp av Azure SDK för Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# Hantera Azure Data Lake Analytics med hjälp av Azure SDK för Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure SDK för Node.js kan användas för att hantera Azure Data Lake Analytics-konton, -jobb och -kataloger. Om du vill se hanteringsämnen med hjälp av andra verktyg klickar du på flikväljaren ovan.

Just nu stöds:

  *  **Node.js-version: 0.10.0 eller högre**
  *  **REST API-version för konto: 2015-10-01-preview**
  *  **REST API-version för katalog: 2015-10-01-preview**
  *  **REST API-version för jobb: 2016-03-20-preview**

## Funktioner

- Kontohantering: skapa, hämta, visa, uppdatera och ta bort.
- Jobbhantering: skicka, hämta, lista, avbryta.
- Kataloghantering: hämta, visa, skapa (hemligheter), uppdatera (hemligheter), ta bort (hemligheter).

## Så här installerar du

```bash
npm install azure-arm-datalake-analytics
```

## Autentisera med hjälp av Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Skapa Data Lake Analytics-klient

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## Skapa ett Data Lake Analytics-konto

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Hämta en lista över jobb

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Hämta en lista över databaser i katalogen Data Lake Analytics
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Se även

- [Microsoft Azure SDK för Node.js](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK för Node.js – Data Lake Store Management](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)


<!--HONumber=Jun16_HO2-->


