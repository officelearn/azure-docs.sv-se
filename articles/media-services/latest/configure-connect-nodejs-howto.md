---
title: Ansluta till Azure Media Services v3 - API för Node.js
description: Lär dig hur du ansluter till Media Services v3-API med Node.js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 40880a2c28ce28a671930ef8837082247e61e24b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733138"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ansluta till Media Services v3 - API för Node.js

Den här artikeln visar hur du ansluter till Azure Media Services v3 node.js SDK med det tjänstens huvudnamn i metoden.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Installera [Node.js](https://nodejs.org/en/download/).
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Var noga med att komma ihåg resursgruppens namn och namnet på Media Services-konto.

## <a name="create-packagejson"></a>Skapa package.json

1. Skapa en package.json-fil med redigeringsprogram du föredrar.
1. Öppna filen och klistra in följande kod:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Följande paket måste anges:

|Paket|Beskrivning|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Om du vill kontrollera att du använder det senaste Azure Media Services-paketet, kontrollera [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Lagrings-SDK. Används när du överför filer till tillgångar.|
|`ms-rest-azure`| Används för att logga in.|

Du kan köra följande kommando för att kontrollera att du använder det senaste paketet:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Ansluta till Node.js-klient

1. Skapa en .js-fil med redigeringsprogram du föredrar.
1. Öppna filen och klistra in följande kod.
1. Ange värden i avsnittet ”endpoint-config” till värden som du fick från [åtkomst API: er](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Köra appen

Öppna en kommandotolk. Bläddra till den exempel katalog och kör följande kommandon:

```
npm install 
node index.js
```

## <a name="see-also"></a>Se också

- [Media Services-koncepten](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Nästa steg

Utforska Media Services [Node.js-referensdokumentationen](https://aka.ms/ams-v3-nodejs-ref) och titta på de [exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials) som visar hur du använder Media Services API med node.js.

