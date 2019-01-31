---
title: 'Snabbstart: Anropa slutpunkten för Anpassad Bing-sökning med hjälp av C# SDK | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Konfigurera C#-konsolprogrammet för SDK:t för anpassad sökning.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 55a9982026e118e3c375908879967831a8121ede
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178247"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Snabbstart: Anropa slutpunkten för Anpassad Bing-sökning med hjälp av C# SDK 

Använd den här snabbstarten till att börja begära sökresultat från din instans av Anpassad Bing-sökning med hjälp av C# SDK. Även om Anpassad Bing-sökning har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK för Anpassad Bing-sökning ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En instans av anpassad Bing-sökning. Gå till [Snabbstart: Skapa din första instans av anpassad Bing-sökning](quick-start.md) för mer information.
- Microsoft [.Net Core](https://www.microsoft.com/net/download/core)
- Valfri version av [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Om du använder Linux/Mac OS kan det här programmet köras med [Mono](http://www.mono-project.com/).
- [NuGet-paketet för anpassad sökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installerat. 
    - Från Solution Explorer i Visual Studio högerklickar du på projektet och väljer `Manage NuGet Packages` på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-paketet. När du installerar NuGet-paketet för anpassad sökning installeras även följande sammansättningar:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


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
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
