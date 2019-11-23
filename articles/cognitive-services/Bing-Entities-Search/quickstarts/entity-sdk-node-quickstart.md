---
title: 'Quickstart: Send a search request with the SDK for Node.js - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att söka efter entiteter med SDK för entitetssökning i Bing för Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 86c0507c3796693f29170f6059d7774312c2b3f0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323836"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Quickstart: Send a search request with the Bing Entity Search SDK for Node.js

Använd den här snabbstarten om du vill börja söka efter entiteter med SDK för entitetssökning i Bing för Node.js. Även om entitetssökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så är SDK:t ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Krav

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).

* [SDK för entitetssökning i Bing för Node.js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Så här installerar du SDK för entitetssökning i Bing:

1. Kör `npm install ms-rest-azure` i utvecklingsmiljön.
2. Kör `npm install azure-cognitiveservices-entitysearch` i utvecklingsmiljön.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i valfri IDE eller redigeringsprogram och lägg till följande krav. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Skapa en instans av `CognitiveServicesCredentials` med hjälp av din prenumerationsnyckel. Skapa sedan en instans av sökklienten med den.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Skicka en begäran och ta emot ett svar

1. Skicka en begäran för entitetssökning med `entitiesOperations.search()`. När du har fått ett svar skriver du ut `queryContext`, antalet returnerade resultat och en beskrivning av det första resultatet.
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API:et för entitetssökning i Bing?](../overview.md )