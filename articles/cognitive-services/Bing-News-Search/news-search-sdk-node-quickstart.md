---
title: 'Snabbstart: Utföra en nyhetssökning – SDK för nyhetssökning i Bing för Node.js'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att söka efter nyheter med SDK för nyhetssökning i Bing för Node.js och bearbeta sedan svaret.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 8f45e0921748092af4702e03d557e52153a78ec0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258670"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Snabbstart: Utföra en nyhetssökning med SDK för nyhetssökning i Bing för Node.js

Använd den här snabbstarten om du vill börja söka efter nyheter med SDK för nyhetssökning i Bing för Node.js. Även om Nyhetssökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Node.js](https://nodejs.org/en/)

Så här skapar du ett konsolprogram med API för nyhetssökning i Bing:
1. Kör `npm install ms-rest-azure` i utvecklingsmiljön.
2. Kör `npm install azure-cognitiveservices-newssearch` i utvecklingsmiljön.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en instans av `CognitiveServicesCredentials`. Skapa variabler för din prenumerationsnyckel och sökvillkor.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. instantiera klienten:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Skicka en sökfråga

3. Använd klienten för att söka med en frågeterm, i det här fallet ”Winter Olympics”:
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

Koden skriver ut `result.value`-objekt till konsolen utan parsning av texten. I resultaten, om sådana finns för respektive kategori, ingår:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
[Skapa en enkelsidig webbapp](tutorial-bing-news-search-single-page-app.md)
