---
title: 'Snabb start: anropa din Anpassad sökning i Bing-slutpunkt med C# | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att börja begära sökresultat från instansen av anpassad Bing-sökning i C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 0abd03338982c866c92a8462e1e840651a9c2759
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101790"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Snabb start: anropa din Anpassad sökning i Bing slut punkt med hjälp av C # 

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Använd den här snabb starten för att lära dig hur du begär Sök Resultat från Anpassad sökning i Bing-instansen. Även om det här programmet är skrivet i C# är API för anpassad Bing-sökning en webb tjänst för RESTful som är kompatibel med de flesta programmeringsspråk. Käll koden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Förutsättningar

- En instans av anpassad Bing-sökning. Mer information finns i [snabb start: skapa din första anpassad sökning i Bing-instans](quick-start.md).
- [Microsoft .net Core](https://www.microsoft.com/net/download/core).
- En version av [Visual Studio 2019 eller senare](https://www.visualstudio.com/downloads/).
- Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
- [Anpassad sökning i Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) NuGet-paketet. 

   Installera det här paketet i Visual Studio: 
     1. Högerklicka på ditt projekt i **Solution Explorer** och välj sedan **Hantera NuGet-paket** . 
     2. Sök efter och välj *Microsoft. Azure. CognitiveServices. search. CustomSearch* och installera sedan paketet.

   När du installerar Anpassad sökning i Bing NuGet-paketet installerar Visual Studio även följande paket:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft. rest. ClientRuntime. Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt C#-konsolprogram i Visual Studio. Lägg sedan till följande paket i projektet:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Skapa följande klasser för att lagra Sök resultaten som returneras av API för anpassad Bing-sökning:

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

3. I projektets huvud metod skapar du följande variabler för din API för anpassad Bing-sökning prenumerations nyckel, Sök instansens anpassade konfigurations-ID och Sök villkor:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Konstruera URL: en för begäran genom att lägga till sökordet i `q=` Frågeparametern och Sök instansens anpassade konfigurations-ID till `customconfig=` parametern. Avgränsa parametrarna med ett et-tecken ( `&` ). För `url` variabelvärdet kan du använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

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

- Iterera över svarsobjektet för att visa information om varje sökresultat, inklusive dess namn, URL och webbsidans senaste crawlningsdatum.

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
