---
title: Snabbstart för JavaScript-klientbibliotek för Bing Video Search JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: b642d981b79d13857881ec8cc5796e6365003ace
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289765"
---
Använd den här snabbstarten för att börja söka efter nyheter med klientbiblioteket Bing Video Search för JavaScript. Bing Video Search har ett REST API som är kompatibelt med de flesta programmeringsspråk, men klientbiblioteket är ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Den innehåller fler kommentarer och funktioner.

## <a name="prerequisites"></a>Krav

- [Node.js](https://www.nodejs.org/)

Så här konfigurerar du ett konsolprogram med klientbiblioteket Bing Video Search:
* Kör `npm install ms-rest-azure` i utvecklingsmiljön.
* Kör `npm install azure-cognitiveservices-videosearch` i utvecklingsmiljön.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i din favorit-IDE eller redigerare och lägg till ett `require()` uttryck för klientbiblioteket och `CognitiveServicesCredentials` modulen Bing Video Search. Skapa en variabel för din prenumerationsnyckel. 
    
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
> [Skapa en webbapp med en sida](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Se även 

* [Vad är API:et för videosökning i Bing?](../../overview.md)
* [Kognitiva tjänster .NET SDK-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)