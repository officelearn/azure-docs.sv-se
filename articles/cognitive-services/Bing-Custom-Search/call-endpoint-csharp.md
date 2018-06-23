---
title: Anropa slutpunkten med hjälp av C# - Bing anpassad sökning - Microsoft kognitiva Services
description: Den här snabbstarten visar hur du begär sökresultatet från anpassad sökning-instans med C# för att anropa Bing anpassad sökning slutpunkten.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: be4cc79d16b9a22124f16878b11ca04a916f98ae
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352857"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Anropa Bing anpassad sökning slutpunkt (C#)

Den här snabbstarten visar hur du begär sökresultatet från anpassad sökning-instans med C# för att anropa Bing anpassad sökning slutpunkten. 

## <a name="prerequisites"></a>Förutsättningar

-  En instans av färdiga att använda anpassad sökning. Se [skapa din första Bing anpassad sökning instans](quick-start.md).
-  [.NET core](https://www.microsoft.com/net/download/core) installerad.
- En [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API: er för Bing Search**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) är tillräcklig för den här snabbstarten. Du måste åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumeration nyckel från instrumentpanelen i Azure.  

  >[!NOTE]  
  >Befintliga Bing anpassad sökning-kunder som har en preview-nyckel som etablerats på eller före den 15 oktober 2017 kommer att kunna använda sina nycklar tills 30 November 2017 eller tills de har förbrukat det maximala antalet frågor tillåts. Därefter kan behöver de för att migrera till den allmänt tillgängliga versionen på Azure. 
 
## <a name="run-the-code"></a>Kör koden

Följ dessa steg om du vill köra det här exemplet:

1. Skapa en mapp för din kod.
2. Navigera till mappen som du skapade från en kommandotolk eller terminal.
3. Kör följande kommandon:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
   ```

4. Kopiera följande kod till Program.cs.
5. Ersätt **din PRENUMERATION nyckel** och **din-anpassad-CONFIG-ID** med din nyckel och konfiguration-ID.

    ``` CSharp
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
6. Skapa programmet med följande kommando. Observera dll-sökväg som refereras av kommandoutdata.
    <pre>
    dotnet build 
    </pre>
7. Kör programmet med hjälp av följande kommando ersätter **sökväg till utdata** med sökvägen refererar till build-steget.
    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Nästa steg
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Markera text med hjälp av decoration markörer](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)
