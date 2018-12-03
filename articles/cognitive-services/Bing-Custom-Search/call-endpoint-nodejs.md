---
title: 'Snabbstart: Anropa slutpunkt med Node.js – Anpassad sökning i Bing'
titlesuffix: Azure Cognitive Services
description: I den här snabbstarten visas hur du begär sökresultat från din anpassade sökinstans genom att låta Node.js anropa slutpunkten för anpassad sökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: c0c97dd52f8fc3ff590c86f32f794beeb00f4b05
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310260"
---
# <a name="quickstart-call-bing-custom-search-endpoint-nodejs"></a>Snabbstart: Anropa slutpunkten för anpassad sökning i Bing (Node.js)

I den här snabbstarten visas hur du begär sökresultat från din anpassade sökinstans genom att låta Node.js anropa slutpunkten för anpassad sökning i Bing. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att slutföra den här snabbstarten:

- En anpassad sökinstans som är redo att användas. Läs [Skapa din första instans för anpassad sökning i Bing](quick-start.md).
- [Node.js](https://www.nodejs.org/) installerat.
- En prenumerationsnyckel. Du kan få en prenumerationsnyckel när du aktiverar din [kostnadsfria utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure (se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).   Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-code"></a>Kör koden

Följ dessa steg om du vill köra det här exemplet:

1. Skapa en mapp för din kod.  
  
2. Gå till mappen som du nyss skapade från en kommandotolk eller terminal.  
  
3. Installera nodmodulen för **begäran**:
    <pre>
    npm install request
    </pre>  
    
4. Skapa en fil med namnet BingCustomSearch.js i mappen du skapade och kopiera följande kod till den. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-CUSTOM-CONFIG-IDID** med din prenumerationsnyckel och ditt konfigurations-ID.  
  
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
- [Konfigurera värdbaserad UI-upplevelse](./hosted-ui.md)
- [Använda dekorationsmarkörer för att markera text](./hit-highlighting.md)
- [Webbsidor för sida](./page-webpages.md)
