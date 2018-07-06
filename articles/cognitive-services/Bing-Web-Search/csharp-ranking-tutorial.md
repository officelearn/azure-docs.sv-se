---
title: Med hjälp av rangordning för att visa sökresultat | Microsoft Docs
description: Visar hur du använder Bing RankingResponse svaret för att visa sökresultat i rangordnas ordning.
services: cognitive-services
author: bradumbaugh
manager: bking
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 05/08/2017
ms.author: brumbaug
ms.openlocfilehash: 7d371a5a44f44ca743294f3f73f84e81d390cdea
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867784"
---
# <a name="build-a-console-app-search-client-in-c"></a>Skapa en app search konsolklient i C#

Den här självstudien visar hur du skapar en enkel .NET Core-konsolapp som tillåter användare att fråga Bing Web Search API och visa översta resultat.

Den här kursen visar hur du:

- Gör en enkel fråga till Bing Web Search API
- Visa frågeresultaten i rankad ordning

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa självstudiekursen behöver du:

- Visual Studio. Om du inte har det, [ladda ned och installera den kostnadsfria Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- En prenumerationsnyckel för Bing Web Search API. Om du inte har någon, [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Skapa ett nytt Console App-projekt

Skapa ett projekt i Visual Studio med `Ctrl`+`Shift`+`N`.

I den **nytt projekt** dialogrutan klickar du på **Visual C# > Windows Classic Desktop > konsolprogram (.NET Framework)**.

Ge programmet namnet **MyConsoleSearchApp**, och klicka sedan på **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Lägg till JSON.net Nuget-paketet i projektet

JSON.net kan du arbeta med JSON-svaren som returneras av API: et. Lägg till dess NuGet-paketet i projektet:

- I **Solution Explorer** högerklickar du på projektet och välj **hantera NuGet-paket...** . 
- På den **Bläddra** fliken och Sök söker efter `Newtonsoft.Json`. Välj den senaste versionen och klicka sedan på **installera**. 
- Klicka på den **OK** knappen på den **granska ändringar** fönster.
- Stäng Visual Studio-flik som heter **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Lägg till en referens till System.Web

Den här kursen används den `System.Web` sammansättningen. Lägg till en referens till den här sammansättningen i projektet:

- I **Solution Explorer**, högerklicka på **referenser** och välj **Lägg till referens...**
- Välj **sammansättningar > Framework**, bläddrar sedan nedåt och kontrollera **System.Web**
- Välj **OK**

## <a name="add-some-necessary-using-statements"></a>Lägga till vissa nödvändiga using-satser

Koden i den här självstudien kräver tre ytterligare med hjälp av uttryck. Lägg till dessa instruktioner under den befintliga `using` uttryck överst i **Program.cs**: 

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Be användaren ange en fråga

I **Solution Explorer**öppnar **Program.cs**. Uppdatera den `Main()` metoden:

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

Den här metoden:

- Frågar användaren om en fråga
- Anrop `RunQueryAndDisplayResults(userQuery)` att köra frågan och visa resultaten
- Väntar på användarindata för att förhindra att omedelbart stänga konsolfönstret.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Sök efter frågeresultaten med hjälp av Bing Web Search API

Lägg sedan till en metod som frågar API: et och visar resultatet:

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

Den här metoden:

- Skapar en `HttpClient` att fråga efter API för webbsökning
- Anger den `Ocp-Apim-Subscription-Key` HTTP-huvud som Bing använder för att autentisera begäran
- Kör begäran och använder JSON.net för att deserialisera resultaten
- Anrop `DisplayAllRankedResults(responseObjects)` att visa alla resultat i rankad ordning

Se till att ange värdet för `Ocp-Apim-Subscription-Key` till din prenumerationsnyckel.

## <a name="display-ranked-results"></a>Visa översta resultat

Ta en titt på ett exempel web search-svar innan du visar hur du vill visa resultatet i rankad ordning: 

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

Den `rankingResponse` JSON-objekt ([dokumentation](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)) beskriver lämplig visningsordningen för sökresultat. Det innehåller en eller flera av följande, prioriterad grupper: 

- `pole`: Sökresultaten till får mest synliga behandling (till exempel visas ovanför den likriktade och sidopanelen).
- `mainline`: Sökresultaten ska visas i den likriktade.
- `sidebar`: Sökresultaten ska visas i sidopanelen. Om det finns ingen sidpanel, kan du visa resultatet under den likriktade.

Rangordning svaret JSON kan innehålla en eller flera av grupperna.

I **Program.cs**, lägger du till följande metod om du vill visa resultatet i korrekt rankad ordning:

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

Den här metoden:

- Slinga över den `rankingResponse` grupper som innehåller svaret
- Visar objekt i varje grupp genom att anropa `DisplaySpecificResults(...)` 

I **Program.cs**, Lägg till följande två metoder:

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

Dessa metoder fungerar tillsammans för att mata ut sökresultatet till konsolen.

## <a name="run-the-application"></a>Köra programmet

Kör appen. Utdata bör se ut ungefär så här:

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

Läs mer om [med rangordning för att visa resultat](rank-results.md).