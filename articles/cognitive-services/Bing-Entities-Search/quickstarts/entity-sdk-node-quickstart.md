---
title: 'Snabbstart: Skicka en sökbegäran med SDK för entitetssökning i Bing för Node.js'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att söka efter entiteter med SDK för entitetssökning i Bing för Node.js
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 015a2d344b066bd7b65c3228a2795c3395793f2b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097038"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Snabbstart: Skicka en sökbegäran med SDK för entitetssökning i Bing för Node.js

Använd den här snabbstarten om du vill börja söka efter entiteter med SDK för entitetssökning i Bing för Node.js. Även om entitetssökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Förutsättningar

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