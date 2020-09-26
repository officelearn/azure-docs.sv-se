---
title: Snabb start för Videosökning i Bing Java Script Client library
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: fa1ecb24649d9355e6104bff8add5430fdd64c3c
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376666"
---
Använd den här snabb starten för att börja söka efter nyheter med Videosökning i Bing klient bibliotek för Java Script. Även om Videosökning i Bing har en REST API som är kompatibel med de flesta programmeringsspråk, är klient biblioteket ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Den innehåller fler kommentarer och funktioner.

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).
* [Videosökning I Bing SDK för Java Script](https://www.npmjs.com/package/@azure/cognitiveservices-videosearch)
     *  Installera genom att köra `npm install @azure/cognitiveservices-videosearch`
* `CognitiveServicesCredentials`Klassen från `@azure/ms-rest-azure-js` paketet för att autentisera klienten.
     * Installera genom att köra `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i din favorit-IDE eller-redigerare och Lägg till en `require()` instruktion för videosökning i Bing klient bibliotek och `CognitiveServicesCredentials` modul. Skapa en variabel för din prenumerationsnyckel. 
    
    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('@azure/cognitiveservices-videosearch');
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
> [Skapa en webbapp med en sida](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Se även 

* [Vad är API:et för videosökning i Bing?](../../overview.md)
* [Kognitiva tjänster .NET SDK-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)