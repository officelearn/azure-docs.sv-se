---
title: 'Snabbstart: Skicka en sökbegäran till REST API för entitetssökning i Bing med hjälp av C#'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten om du vill skicka en begäran till REST API för entitetssökning i Bing med hjälp av C# och få ett JSON-svar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: e41d99b4ddee41778e97aac9b5b947c0aa0eac5a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404953"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Snabbstart: Skicka en sökbegäran till REST API för entitetssökning i Bing med hjälp av C#

Använd den här snabbstarten för att göra ditt första anrop till API för entitetssökning i Bing och visa JSON-svaret. Det här enkla C#-programmet skickar en nyhetssökfråga till API:et och visar svaret. Källkoden för det här programmet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Även om det här programmet är skrivet i C#, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.


## <a name="prerequisites"></a>Förutsättningar

- En version av [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/).

- [Json.NET](https://www.newtonsoft.com/json) framework, tillgänglig som ett NuGet-paket. Så här installerar du NuGet-paketet i Visual Studio:

   1. Högerklicka på ditt projekt i **Solution Explorer**.
   2. Välj **Hantera NuGet-paket**.
   3. Sök efter *Newtonsoft. JSON* och installera paketet.

- Om du använder Linux/MacOS kan du köra det här programmet med hjälp av [mono](https://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. skapa en ny C#-konsollösning i Visual Studio. Lägg sedan till följande namnrymder i huvudkodfilen.
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Skapa en ny klass och lägg till variabler för API-slutpunkten, din prenumerationsnyckel samt den fråga som du vill söka med.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Skicka en begäran och få API-svaret

1. I klassen skapar du en funktion med namnet `Search()`. Skapa ett nytt `HttpClient`-objekt och lägg till prenumerationsnyckeln i `Ocp-Apim-Subscription-Key`-rubriken.

   1. Skapa URI för din begäran genom att kombinera värd och sökväg. Lägg sedan till din marknad och URL-koda frågan.
   2. Vänta på `client.GetAsync()` för att få ett HTTP-svar och lagra sedan json-svaret genom att vänta på `ReadAsStringAsync()`.
   3. Formatera JSON-strängen med `JsonConvert.DeserializeObject()` och skriv ut den till-konsolen.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

2. I main-metoden för programmet anropar du funktionen `Search()`.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API:et för entitetssökning i Bing?](../overview.md )
* [Referens för API för entitetsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
