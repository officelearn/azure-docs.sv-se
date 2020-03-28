---
title: Snabbstart för Bing Video Search C#-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: d50e1acd104916d68f7fbb84ff568cf4efc0b46b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289758"
---
Använd den här snabbstarten för att börja söka efter nyheter med Bing Video Search-klientbiblioteket för C#. Bing Video Search har ett REST API som är kompatibelt med de flesta programmeringsspråk, men klientbiblioteket är ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) med ytterligare anteckningar och funktioner.

## <a name="prerequisites"></a>Krav

* Alla utgåvor av [Visual Studio 2017 eller senare](https://visualstudio.microsoft.com/downloads/).
* Den Json.NET ramen, tillgänglig [som en NuGet paket](https://www.nuget.org/packages/Newtonsoft.Json/).

Om du vill lägga till klientbiblioteket för Bing Video Search i projektet väljer du **Hantera NuGet-paket** från **Solution Explorer** i Visual Studio. Lägg till paketet `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

När du installerar [[SDK-paketet för NuGet-videosökning]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) installeras även följande beroenden:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa en ny C#-konsollösning i Visual Studio. Lägg sedan till följande i huvudkodfilen.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Instantiera klienten genom att skapa ett nytt `ApiKeyServiceClientCredentials`-objekt med prenumerationsnyckeln och anropa konstruktorn.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Skicka en sökbegäran och bearbeta resultatet

1. Använd klienten för att skicka en sökbegäran. Använd ”SwiftKey” för sökfrågan.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Om alla resultat returnerades hämtar du det första med `videoResults.Value[0]`. Skriv sedan ut videons ID, rubrik och url.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp med en sida](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Se även 

* [Vad är API:et för videosökning i Bing?](../../overview.md)
* [Kognitiva tjänster .NET SDK-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
