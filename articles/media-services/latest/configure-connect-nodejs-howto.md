---
title: Ansluta till Azure Media Services v3 API – Node.js
description: Den här artikeln visar hur du ansluter till Media Services v3-API med Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: c6ea238edd68413646dda59b22d1c0dc2557d57e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916839"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ansluta till Media Services v3 API – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du ansluter till Azure Media Services v3-node.js SDK med hjälp av inloggnings metoden för tjänstens huvud namn.

## <a name="prerequisites"></a>Förutsättningar

- Installera [Node.js](https://nodejs.org/en/download/).
- [Skapa ett Media Services-konto](./create-account-howto.md). Glöm inte att komma ihåg resurs gruppens namn och namnet på Media Services kontot.

> [!IMPORTANT]
> Granska [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Skapa package.jspå

1. Skapa en package.jspå en fil med hjälp av din favorit redigerare.
1. Öppna filen och klistra in följande kod:

   Se till att hämta den senaste versionen av [AZUREMEDIASERVICES SDK för Java Script](https://www.npmjs.com/package/@azure/arm-mediaservices).

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^8.0.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Följande paket ska anges:

|Paket|Beskrivning|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Kontrol lera att du använder det senaste Azure Media Servicess paketet genom att markera [NPM installera Azure-arm-Media Services](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Storage SDK. Används vid överföring av filer till till gångar.|
|`ms-rest-azure`| Används för att logga in.|

Du kan köra följande kommando för att kontrol lera att du använder det senaste paketet:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Anslut till Node.js-klient

1. Skapa en. js-fil med hjälp av din favorit redigerare.
1. Öppna filen och klistra in följande kod.
1. Ange värdena i avsnittet "Endpoint config" till värden som du har fått från [åtkomst till API: er](./access-api-howto.md).

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

## <a name="run-your-app"></a>Kör appen

Öppna en kommandotolk. Bläddra till exempel katalogen och kör följande kommandon:

```
npm install 
node index.js
```

## <a name="see-also"></a>Se även

- [Media Services begrepp](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Nästa steg

Utforska Media Services [Node.js-referensdokumentationen](/javascript/api/overview/azure/mediaservices/management) och titta på de [exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials) som visar hur du använder Media Services API med node.js.
