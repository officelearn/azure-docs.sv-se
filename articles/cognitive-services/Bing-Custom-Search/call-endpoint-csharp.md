---
title: 'Snabbstart: Anropa slutpunkt med hjälp av C# – Anpassad sökning i Bing'
titlesuffix: Azure Cognitive Services
description: Den här snabbstarten visar hur du begär sökresultat från din anpassade sökinstans genom att använda C# för att anropa slutpunkten för Anpassad sökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: maheshb
ms.openlocfilehash: 3a7ba0f464dc82751df5daabd4226fc521fe6916
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316199"
---
# <a name="quickstart-call-bing-custom-search-endpoint-c"></a>Snabbstart: Anropa slutpunkten för anpassad sökning i Bing (C#)

Den här snabbstarten visar hur du begär sökresultat från din anpassade sökinstans genom att använda C# för att anropa slutpunkten för Anpassad sökning i Bing. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att slutföra den här snabbstarten:

- En anpassad sökinstans som är redo att användas. Se sidan om att [skapa din första instans för Anpassad sökning i Bing](quick-start.md).
- [.NET Core](https://www.microsoft.com/net/download/core) installerat.
- En prenumerationsnyckel. Du kan få en prenumerationsnyckel när du aktiverar din [kostnadsfria utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure (se [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).   Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="run-the-code"></a>Kör koden

Följ dessa steg om du vill köra det här exemplet:

1. Skapa en mapp för din kod.  
  
2. Gå till mappen som du nyss skapade från en kommandotolk eller terminal.  
  
3. Kör följande kommandon:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
    ```
  
4. Kopiera följande kod till Program.cs. Ersätt **YOUR-SUBSCRIPTION-KEY** och **YOUR-CUSTOM-CONFIG-IDID** med din prenumerationsnyckel och ditt konfigurations-ID.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    
    namespace bing_custom_search_example_dotnet
    {
        class Program
        {
            static void Main(string[] args)
            {
                var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
                var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
                var searchTerm = args.Length > 0 ? args[0]: "microsoft";            
    
                var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                    "q=" + searchTerm +
                    "&customconfig=" + customConfigId;
    
                var client = new HttpClient();
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                var httpResponseMessage = client.GetAsync(url).Result;
                var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
                BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
                
                for(int i = 0; i < response.webPages.value.Length; i++)
                {                
                    var webPage = response.webPages.value[i];
                    
                    Console.WriteLine("name: " + webPage.name);
                    Console.WriteLine("url: " + webPage.url);                
                    Console.WriteLine("displayUrl: " + webPage.displayUrl);
                    Console.WriteLine("snippet: " + webPage.snippet);
                    Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
                    Console.WriteLine();
                }            
            }
        }
    
        public class BingCustomSearchResponse
        {        
            public string _type{ get; set; }            
            public WebPages webPages { get; set; }
        }
    
        public class WebPages
        {
            public string webSearchUrl { get; set; }
            public int totalEstimatedMatches { get; set; }
            public WebPage[] value { get; set; }        
        }
    
        public class WebPage
        {
            public string name { get; set; }
            public string url { get; set; }
            public string displayUrl { get; set; }
            public string snippet { get; set; }
            public DateTime dateLastCrawled { get; set; }
            public string cachedPageUrl { get; set; }
            public OpenGraphImage openGraphImage { get; set; }        
        }
        
        public class OpenGraphImage
        {
            public string contentUrl { get; set; }
            public int width { get; set; }
            public int height { get; set; }
        }
    }
    ```
6. Skapa programmet med hjälp av följande kommando. Observera den DLL-sökväg som refereras av kommandoutdata.

    <pre>
    dotnet build 
    </pre>
    
7. Kör programmet med hjälp av följande kommando och ersätt **PATH TO OUTPUT** (sökvägen till utdata) med den DLL-sökväg som refereras till i steg 6.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Nästa steg
- [Konfigurera värdbaserad UI-upplevelse](./hosted-ui.md)
- [Använda dekorationsmarkörer för att markera text](./hit-highlighting.md)
- [Webbsidor för sida](./page-webpages.md)
