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
ms.openlocfilehash: 73c31c7175bd4dfcb182fb76784937c176ac7702
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977884"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Anropa anpassad sökning i Bing slutpunkt (Node.js)

Den här snabbstarten visar hur du begär sökresultat från din anpassade Sökinstans med Node.js för att anropa anpassad sökning i Bing-slutpunkten. 

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra den här snabbstarten:

- En färdiga att använda anpassad Sökinstans. Se [skapar din första Bing Custom Search-instans](quick-start.md).
- [Node.js](https://www.nodejs.org/) installerad.
- En prenumerationsnyckel. Du kan få en prenumerationsnyckel när du aktiverar din [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), eller du kan använda en betald prenumerationsnyckel från instrumentpanelen i Azure (se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    

## <a name="run-the-code"></a>Kör koden

Följ dessa steg om du vill köra det här exemplet:

1. Skapa en mapp för din kod.  
  
2. Navigera till mappen som du nyss skapade från en kommandotolk eller terminal.  
  
3. Installera den **begäran** nod-modulen:
    <pre>
    npm install request
    </pre>  
    
4. Skapa en fil med namnet BingCustomSearch.js i den mapp du skapade och kopiera följande kod till den. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-anpassad-CONFIG-ID** med din prenumerationsnyckel och konfiguration-ID.  
  
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
  
6. Kör koden med följande kommando:  
  
    ```    
    node BingCustomSearch.js
    ``` 

## <a name="next-steps"></a>Nästa steg
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Använda decoration markörer för att markera text](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)
