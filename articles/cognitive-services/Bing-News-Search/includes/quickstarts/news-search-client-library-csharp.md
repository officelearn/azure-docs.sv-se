---
title: Bing News Search C# klientbibliotek snabbstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: fd43fcb1b5fb70862ed1c1fa5111f0893495b437
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503890"
---
Använd den här snabbstarten för att börja söka efter nyheter med klientbiblioteket Bing News Search för C#. Bing News Search har ett REST API som är kompatibelt med de flesta programmeringsspråk, men klientbiblioteket är ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch).

## <a name="prerequisites"></a>Krav

* Alla utgåvor av [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/).
* [Json.NET](https://www.newtonsoft.com/json) framework, tillgänglig som ett NuGet-paket.
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).

* [NuGet-paket för SDK för Nyhetssökning i Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0). Installering av det här paketet installerar även följande:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Om du vill konfigurera ett konsolprogram med klientbiblioteket `Manage NuGet Packages` Bing News Search bläddrar du till alternativet från Solution Explorer i Visual Studio.  Lägg till paketet `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa en ny C#-konsollösning i Visual Studio. Lägg sedan till följande i huvudkodfilen.
    
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    ```

2. Skapa en variabel för din API-nyckel, en sökterm, och sedan skapa sedan en instans av klienten för nyhetssökning med den.

    ```csharp
    var key = "YOUR-ACCESS-KEY";
    var searchTerm = "Quantum Computing";
    var client = new NewsSearchClient(new ApiKeyServiceClientCredentials(key));
    ```

## <a name="send-a-request-and-parse-the-result"></a>Skicka en begäran och tolka resultatet

1. Du kan använda klienten för att skicka en sökbegäran till tjänsten Nyhetssökning i Bing:
    ```csharp
    var newsResults = client.News.SearchAsync(query: searchTerm, market: "en-us", count: 10).Result;
    ```

2. Om alla resultat returnerades ska du parsa dem:

    ```csharp
    if (newsResults.Value.Count > 0)
    {
        var firstNewsResult = newsResults.Value[0];
    
        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
        Console.WriteLine($"News result count: {newsResults.Value.Count}");
        Console.WriteLine($"First news name: {firstNewsResult.Name}");
        Console.WriteLine($"First news url: {firstNewsResult.Url}");
        Console.WriteLine($"First news description: {firstNewsResult.Description}");
        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
        Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
    }
    
    else
    {
        Console.WriteLine("Couldn't find news results!");
    }
    Console.WriteLine("Enter any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ensidig webbapp](../../tutorial-bing-news-search-single-page-app.md)
