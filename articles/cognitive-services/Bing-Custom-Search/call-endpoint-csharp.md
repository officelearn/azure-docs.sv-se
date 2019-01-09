---
title: 'Snabbstart: Anropa slutpunkten för anpassad Bing-sökning med hjälp av C# | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att börja begära sökresultat från instansen av anpassad Bing-sökning i C#.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: maheshb
ms.openlocfilehash: 764a6a060a3ce2c7af9e4051b02ad45d14d27900
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557826"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Snabbstart: Anropa slutpunkten för anpassad Bing-sökning med hjälp av C# 

Använd den här snabbstarten till att börja begära sökresultat från instansen av anpassad Bing-sökning. Även om det här programmet är skrivet i C#, är API:et för anpassad Bing-sökning en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

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

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny C#-konsolapp i Visual Studio. Lägg sedan till följande paket i projektet.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Skapa följande klasser för att lagra det returnerade sökresultatet från API:et för anpassad Bing-sökning.

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. I main-metoden för projektet skapar du variabler för prenumerationsnyckeln för API:et för anpassad Bing-sökning, sökinstansens ID för anpassad konfiguration och en sökterm.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Konstruera en begäran-URL genom att lägga till söktermen i `q=`-frågeparametern och sökinstansens ID för anpassad konfiguration i `customconfig=`. Avgränsa parametrarna med ett `&`-tecken. 

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Skicka och ta emot en sökbegäran 

1. Skapa en begäranklient och lägg till prenumerationsnyckeln i `Ocp-Apim-Subscription-Key`-rubriken.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Utför sökbegäran och få svaret som ett JSON-objekt.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>Bearbeta och visa svaren

1. Iterera över svarsobjektet för att visa information om varje sökresultat, inklusive dess namn, URL och webbsidans senaste crawlningsdatum.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](./tutorials/custom-search-web-page.md)