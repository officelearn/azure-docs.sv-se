---
title: Hantera Azure Data Lake Analytics med hjälp av Azure SDK för Node.js
description: Den här artikeln beskriver hur du använder Azure SDK för Node.js till att hantera Data Lake Analytics-konton, datakällor, jobb och användare.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 0603a60ea73d47dd6107ee80afc5c776ff8c83bc
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047119"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Hantera Azure Data Lake Analytics med hjälp av Azure SDK för Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Artikeln beskriver hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av en app som skrivits med Azure SDK för Node.js. 

Följande versioner stöds:
* **Node.js-version: 0.10.0 eller högre**
* **REST API-version för konto: 2015-10-01-preview**
* **REST API-version för katalog: 2015-10-01-preview**
* **REST API-version för jobb: 2016-03-20-preview**

## <a name="features"></a>Funktioner
* Kontohantering: skapa, hämta, visa, uppdatera och ta bort.
* Jobbhantering: skicka, hämta, visa och avbryta.
* Kataloghantering: hämta och visa.

## <a name="how-to-install"></a>Så här installerar du
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Autentisera med hjälp av Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Skapa Data Lake Analytics-klient
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto
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

## <a name="get-a-list-of-jobs"></a>Hämta en lista över jobb
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

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Hämta en lista över databaser i katalogen Data Lake Analytics
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

## <a name="see-also"></a>Se också
* [Microsoft Azure SDK för Node.js](https://github.com/azure/azure-sdk-for-node)
* [Microsoft Azure SDK för Node.js – Data Lake Store Management](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

