---
title: Använda rang för att visa sökresultat
titleSuffix: Azure Cognitive Services
description: Visar hur du använder svaret Bing RankingResponse för att visa sökresultat i rangordning.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1c8e0bb136fddeb84dc991e63a761378b38cc470
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382335"
---
# <a name="build-a-console-app-search-client-in-c"></a>Skapa en konsolappsökklient i C #

Den här självstudien visar hur du skapar en enkel .NET Core-konsolapp som tillåter användare att fråga Bing Web Search API och visa rankade resultat.

Den här självstudien visar hur du:

- Gör en enkel fråga till API:et för webbsökning på Bing
- Visa frågeresultat i rangordning

## <a name="prerequisites"></a>Krav

För att följa med handledningen, behöver du:

- Visual Studio. Om du inte har det kan [du hämta och installera den kostnadsfria Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- En prenumerationsnyckel för API:et för webbsökning på Bing. Om du inte har redan har en kan du registrera dig för [en kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Skapa ett nytt konsolappprojekt

Skapa ett projekt i Visual Studio med `Ctrl`+`Shift`+`N`.

Klicka på **Visual C# > Windows Classic Desktop > Console App (.NET Framework)** i dialogrutan **Nytt projekt** .

Namnge programmet **MyConsoleSearchApp**och klicka sedan på **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Lägg till JSON.net Nuget-paketet i projektet

JSON.net kan du arbeta med JSON-svaren som returneras av API:et. Lägg till dess NuGet-paket i projektet:

- Högerklicka på projektet i **Solution Explorer** och välj **Hantera NuGet-paket...**.
- Sök efter på fliken `Newtonsoft.Json`Bläddra på fliken **Bläddra.** Markera den senaste versionen och klicka sedan på **Installera**.
- Klicka på knappen **OK** i fönstret **Granska ändringar.**
- Stäng fliken Visual Studio med namnet **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Lägga till en referens till System.Web

Den här självstudien `System.Web` förlitar sig på sammansättningen. Lägg till en referens till den här sammansättningen i projektet:

- Högerklicka på **Referenser** i **Solution Explorer**och välj Lägg **till referens...**
- Välj **Sammansättningar > Framework**och rulla sedan nedåt och kontrollera **System.Web**
- Välj **OK**

## <a name="add-some-necessary-using-statements"></a>Lägg till några nödvändiga med hjälp av satser

Koden i den här självstudien kräver ytterligare tre med hjälp av satser. Lägg till dessa `using` uttalanden under de befintliga uttalandena högst upp **i Program.cs:**

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Be användaren om en fråga

Öppna **Program.cs i** **Solution Explorer**. Uppdatera `Main()` metoden:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Denna metod:

- Fråga användaren om en fråga
- Anrop `RunQueryAndDisplayResults(userQuery)` för att köra frågan och visa resultaten
- Väntar på användarindata för att förhindra att konsolfönstret stängs omedelbart.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Söka efter frågeresultat med api:et för webbsökning på Bing

Lägg sedan till en metod som frågar API:et och visar resultaten:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Denna metod:

- Skapar en `HttpClient` fråga om webbsök-API:et
- Anger `Ocp-Apim-Subscription-Key` HTTP-huvudet som Bing använder för att autentisera begäran
- Kör begäran och använder JSON.net för att avserialisera resultaten
- Samtal `DisplayAllRankedResults(responseObjects)` för att visa alla resultat i rangordning

Se till att ange `Ocp-Apim-Subscription-Key` värdet på din prenumerationsnyckel.

## <a name="display-ranked-results"></a>Visa rankade resultat

Innan du visar hur du visar resultaten i rangordning, ta en titt på ett exempel på webbsökning svar:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

`rankingResponse` JSON-objektet ([dokumentation](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) beskriver lämplig visningsordning för sökresultat. Den innehåller en eller flera av följande prioriterade grupper:

- `pole`: Sökresultaten för att få den mest synliga behandlingen (till exempel ovanför huvudlinjen och sidofältet).
- `mainline`: Sökresultaten som ska visas i huvudlinjen.
- `sidebar`: Sökresultaten som ska visas i sidofältet. Om det inte finns något sidofält, visa resultaten under huvudlinjen.

Rangordningen svar JSON kan innehålla en eller flera av grupperna.

I **Program.cs**lägger du till följande metod för att visa resultat i korrekt rangordnad ordning:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Denna metod:

- Loopar över `rankingResponse` de grupper som svaret innehåller
- Visar objekten i varje grupp genom att ringa`DisplaySpecificResults(...)`

I **Program.cs**lägger du till följande två metoder:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Dessa metoder fungerar tillsammans för att mata ut sökresultaten till konsolen.

## <a name="run-the-application"></a>Köra appen

Kör appen. Resultatet bör se ut ungefär så här:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du använder rankning för att visa resultat](rank-results.md).
