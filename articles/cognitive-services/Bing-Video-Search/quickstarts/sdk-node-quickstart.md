---
title: 'Snabbstart: Söka efter videor med hjälp av SDK för videosökning i Bing för Node.js'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att skicka sökbegäranden om video med hjälp av SDK för videosökning i Bing för Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: aahi
ms.openlocfilehash: 12eafca9c673d95813eefcd58d2b3f9ba7b54fd3
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358882"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Snabbstart: Utföra en videosökning med SDK för videosökning i Bing för Node.js

Använd den här snabbstarten till att börja söka efter nyheter med SDK för videosökning i Bing för Node.js. Även om Videosökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så är SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Den innehåller fler kommentarer och funktioner.

## <a name="prerequisites"></a>Förutsättningar

- [Node.js](https://www.nodejs.org/)

Så här skapar du ett konsolprogram med API för videosökning i Bing:
* Kör `npm install ms-rest-azure` i utvecklingsmiljön.
* Kör `npm install azure-cognitiveservices-videosearch` i utvecklingsmiljön.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i valfri IDE eller valfritt redigeringsprogram och lägg till en `require()`-instruktion för SDK för videosökning i Bing samt modulen `CognitiveServicesCredentials`. Skapa en variabel för din prenumerationsnyckel. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Skapa en instans av `CognitiveServicesCredentials` med din nyckel. Använda den sedan för att skapa en instans av videosökningsklienten.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Skicka sökbegäran

1. Använd `client.videosOperations.search()` för att skicka en sökbegäran till API för videosökning i Bing. När sökresultaten returneras använder du `.then()` för att logga resultatet.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp med en sida](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Se också 

* [Vad är API för videosökning i Bing?](../overview.md)
* [Exempel med Cognitive Services SDK för .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)