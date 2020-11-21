---
title: 'Snabb start: analysera en fjärravbildning med REST API och Node.js'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten analyserar du en bild med hjälp av API:et för visuellt innehåll med Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, seo-javascript-september2018, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: c91eb01ff6c2c53514eebf164b28d409021dd8a6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013945"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-nodejs"></a>Snabb start: analysera en fjärravbildning med hjälp av Visuellt innehåll REST API med Node.js

I den här snabb starten ska du analysera en fjärrlagrad avbildning för att extrahera visuella funktioner med hjälp av Visuellt innehåll REST API med Node.js. Med metoden [analysera avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) kan du extrahera visuella funktioner baserat på bild innehåll.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Node.js](https://nodejs.org) 4. x eller senare 
* [npm](https://www.npmjs.com/) 
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* [Skapa miljövariabler](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för nyckel-och slut punkts-URL: en, med namnet respektive `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Så här skapar du och kör exemplet:

1. Installera NPM- [`request`](https://www.npmjs.com/package/request) paketet.
   1. Öppna ett kommandotolksfönster som administratör.
   1. Kör följande kommando:

      ```console
      npm install request
      ```

   1. Stäng kommandotolksfönstret när paketet har installerats.

1. Kopiera följande kod till en textredigerare.
1. Du kan också ersätta värdet för `imageUrl` med webbadressen till en annan bild som du vill analysera.
1. Du kan också ersätta värdet för frågeparametern `language` med ett annat språk.
1. Spara koden som en fil med tillägget `.js`. Exempelvis `analyze-image.js`.
1. Öppna ett kommandotolksfönster.
1. Kör filen i kommandotolken med kommandot `node`. Exempelvis `node analyze-image.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v3.1/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Granska svaret

Ett svar som anger att åtgärden lyckades returneras i JSON. Exemplet parsar och visar ett lyckat svar i kommandotolkens fönster enligt följande exempel:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver filen längre kan du ta bort den och sedan avinstallera npm-paketet `request`. Avinstallera paketet på följande sätt:

1. Öppna ett kommandotolksfönster som administratör.
2. Kör följande kommando:

   ```console
   npm uninstall request
   ```

3. Stäng kommandotolkens fönster när paketet har avinstallerats.

## <a name="next-steps"></a>Nästa steg

Utforska sedan de Visuellt innehåll-API: er som används för att analysera en avbildning, identifiera kändisar och landmärken, skapa en miniatyr och extrahera utskrift och handskriven text.

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d)
