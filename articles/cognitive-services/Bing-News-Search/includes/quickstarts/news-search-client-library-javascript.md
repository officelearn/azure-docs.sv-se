---
title: Snabbstart för Bing News Search JavaScript-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 858e6b9e0e40ab988a4cdf04b31580c1ca28d40a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503897"
---
Använd den här snabbstarten för att börja söka efter nyheter med klientbiblioteket Bing News Search för JavaScript. Bing News Search har ett REST API som är kompatibelt med de flesta programmeringsspråk, men klientbiblioteket är ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Krav

* [Node.js](https://nodejs.org/en/)

Så här konfigurerar du ett konsolprogram med klientbiblioteket Bing News Search:
1. Kör `npm install ms-rest-azure` i utvecklingsmiljön.
2. Kör `npm install azure-cognitiveservices-newssearch` i utvecklingsmiljön.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en instans av `CognitiveServicesCredentials`. Skapa variabler för din prenumerationsnyckel och en sökterm.

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

1. Använd klienten för att söka med en frågeterm, i det här fallet ”Winter Olympics”:
    
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
> [Skapa en ensidig webbapp](../../tutorial-bing-news-search-single-page-app.md)
