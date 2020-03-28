---
title: Snabbstart för Bing Custom Search C#-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ba80d1396b30b61bdfe4c121220429f5a7d994b0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79485970"
---
Kom igång med klientbiblioteket för anpassad sökning på Bing för C#. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Med API:et för anpassad sökning på Bing kan du skapa skräddarsydda, annonsfria sökupplevelser för ämnen som du bryr dig om. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

Använd klientbiblioteket Bing Custom Search för C# för att:
* Hitta sökresultat på webben, från din anpassade Bing-sökningsinstans.

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [Exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Krav

- En instans av anpassad Bing-sökning. Se [Snabbstart: Skapa din första förekomst av anpassad bing-sökning](../../quick-start.md) för mer information.
- Microsoft [.NET-kärna](https://www.microsoft.com/net/download/core)
- Alla utgåvor av [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/)
- Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
- [Paketet Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet. 
    - Från **Solution Explorer** i Visual Studio högerklickar du på projektet och väljer Hantera **NuGet-paket** på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-paketet. När du installerar NuGet-paketet för anpassad sökning installeras även följande sammansättningar:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt C#-konsolprogram i Visual Studio. Lägg sedan till följande paket i projektet.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. I main-metoden för programmet instansierar du sökklienten med din API-nyckel.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Skicka sökbegäran och ta emot ett svar
    
1. Skicka en sökfråga med hjälp av din klients `SearchAsync()`-metod och spara svaret. Ersätt `YOUR-CUSTOM-CONFIG-ID` med instansens konfigurations-ID (du hittar ID:t på [portalen för Anpassad Bing-sökning](https://www.customsearch.ai/)). Det här exemplet söker efter ”Xbox”.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. `SearchAsync()`-metoden returnerar ett `WebData`-objekt. Använd objektet för att iterera genom eventuella `WebPages` som hittades. Den här koden hittar det första webbsidesresultatet och skriver ut webbsidans `Name` och `URL`.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](../../tutorials/custom-search-web-page.md)
