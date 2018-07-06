---
title: Anropa slutpunkten med Node.js – Bing Custom Search - Microsoft Cognitive Services
description: Den här snabbstarten visar hur du begär sökresultat från din anpassade Sökinstans genom att använda Node.js för att anropa anpassad sökning i Bing-slutpunkten.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 5d9391cc486dc868a1a291ccc7095291cddd3e4c
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858467"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Anropa anpassad sökning i Bing slutpunkt (Node.js)

Den här snabbstarten visar hur du begär sökresultat från din anpassade Sökinstans genom att använda Node.js för att anropa anpassad sökning i Bing-slutpunkten. 

## <a name="prerequisites"></a>Förutsättningar
Följande krävs för att slutföra den här snabbstarten:

- En anpassad Sökinstans. Se [skapar din första Bing Custom Search-instans](quick-start.md).

- [Node.js](https://www.nodejs.org/) installerad.

-  [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API: er för Bing-sökresultat**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) är tillräcklig för den här snabbstarten. Du behöver åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumerationsnyckel från instrumentpanelen i Azure.

## <a name="run-the-code"></a>Kör koden

Följ dessa steg för att anropa Bing Custom Search-slutpunkten:

1. Skapa en mapp för din kod.

2. Navigera till mappen som du nyss skapade från en kommandotolk eller terminal.

3. Installera den **begäran** nod-modulen:
    <pre>
    npm install request
    </pre>
    
4. Skapa filen BingCustomSearch.js och kopiera följande kod till den.

5. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-anpassad-CONFIG-ID** med ditt ID för nyckeln och konfigurationsdata (se steg 1).

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
        var searchResponse = JSON.parse(body);
        for(var i = 0; i < searchResponse.webPages.value.length; ++i){
            var webPage = searchResponse.webPages.value[i];
            console.log('name: ' + webPage.name);
            console.log('url: ' + webPage.url);
            console.log('displayUrl: ' + webPage.displayUrl);
            console.log('snippet: ' + webPage.snippet);
            console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
            console.log();
        }
    })
    ```
6. Kör kod med följande kommando.
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>Nästa steg
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Använda decoration markörer för att markera text](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)
