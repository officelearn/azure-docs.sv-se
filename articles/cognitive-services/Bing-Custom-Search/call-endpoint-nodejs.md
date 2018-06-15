---
title: Anropa slutpunkten med hjälp av kognitiva Node.js - Bing anpassad sökning - Microsoft-tjänster
description: Den här snabbstarten visar hur du begär sökresultatet från anpassad sökning-instans med Node.js för att anropa Bing anpassad sökning slutpunkten.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 48fc234e15ce3b9172d766f6fae11b51a017ce70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355731"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Anropa Bing anpassad sökning slutpunkt (Node.js)

Den här snabbstarten visar hur du begär sökresultatet från anpassad sökning-instans med Node.js för att anropa Bing anpassad sökning slutpunkten. 

## <a name="prerequisites"></a>Förutsättningar
Följande krävs för att slutföra den här snabbstarten:

- En anpassad sökning-instans. Se [skapa din första Bing anpassad sökning instans](quick-start.md).

- [Node.js](https://www.nodejs.org/) installerad.

-  [Kognitiva Services API-kontot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API: er för Bing Search**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) är tillräcklig för den här snabbstarten. Du måste åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumeration nyckel från instrumentpanelen i Azure.

## <a name="run-the-code"></a>Kör koden

Följ dessa steg för att anropa Bing anpassad sökning-slutpunkten:

1. Skapa en mapp för din kod.
2. Navigera till mappen som du skapade från en kommandotolk eller terminal.
3. Installera den **begäran** nod modulen:
    <pre>
    npm install request
    </pre>
4. Skapa filen BingCustomSearch.js och kopiera följande kod till den.
5. Ersätt **din PRENUMERATION nyckel** och **din-anpassad-CONFIG-ID** med nyckeln och konfigurationsdata-ID (se steg 1).

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
- [Markera text med hjälp av decoration markörer](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)