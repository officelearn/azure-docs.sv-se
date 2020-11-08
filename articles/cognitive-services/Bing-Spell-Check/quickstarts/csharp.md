---
title: 'Snabb start: kontrol lera stavningen med REST API och C#-Stavningskontroll i Bing'
titleSuffix: Azure Cognitive Services
description: Kom igång med Stavningskontroll i Bing REST API och C# för att kontrol lera stavning och grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 3229091a0effda7837b8e58fe8764910e3b6636d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367003"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Snabb start: kontrol lera stavningen med Stavningskontroll i Bing REST API och C #

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Använd den här snabbstarten för att göra ditt första anrop till REST API för stavningskontroll i Bing. Det här enkla C#-programmet skickar en begäran till API:et och returnerar en lista över föreslagna korrigeringar. 

Även om det här programmet är skrivet i C# är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Käll koden för det här programmet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Förutsättningar

* En version av [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/).
* Newtonsoft.JsNuGet-paketet. 
     
   Installera det här paketet i Visual Studio:

     1. I **Solution Explorer** högerklickar du på lösnings filen.
     1. Välj **Hantera NuGet-paket för lösningen**.
     1. Sök efter *Newtonsoft.Jspå* och installera paketet.

* Om du använder Linux/MacOS kan du köra det här programmet med hjälp av [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa en ny konsol lösning med namnet SpellCheckSample i Visual Studio. Lägg sedan till följande namn rymder i huvud kod filen:
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Skapa variabler för API-slutpunkten, prenumerationsnyckeln och den text som ska stavningskontrolleras. Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Skapa en sträng för dina Sök parametrar: 

   1. Tilldela din marknads kod till- `mkt` parametern med `=` operatorn. Marknads koden är koden för landet/regionen som du gör begäran från. 

   1. Lägg till `mode` parametern med `&` operatorn och tilldela sedan läget för stavnings kontroll. Läget kan vara antingen `proof` (fångar de flesta stavfel/grammatikfel) eller `spell` (fångar de flesta stavfel, men inte lika många grammatiska fel).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Skapa och skicka en begäran om stavningskontroll

1. Skapa en asynkron funktion som heter `SpellCheck()` för att skicka en begäran till API:et. Skapa en ny `HttpClient` och lägg till prenumerationsnyckeln i `Ocp-Apim-Subscription-Key`-rubriken. I funktionen följer du nästa steg.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Skapa URI: n för din begäran genom att lägga till din värd, sökväg och parametrar.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Skapa en lista med ett `KeyValuePair`-objekt som innehåller texten och använd det för att skapa ett `FormUrlEncodedContent`-objekt. Ange rubrikinformationen och använd `PostAsync()` för att skicka begäran.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>Hämta och skriva ut API-svaret

### <a name="get-the-client-id-header"></a>Hämta klient-ID-rubriken

Om svaret innehåller en `X-MSEdge-ClientID`-rubrik hämtar du värdet och skriver ut det.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Hämta svaret

Hämta svaret från API:et. Deserialisera JSON-objektet och skriv ut det till konsolen.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Anropa stavningskontrollfunktionen

I `Main()` projektets funktion anropar du `SpellCheck()` .

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>Kör programmet

Skapa och kör ditt projekt. Om du använder Visual Studio trycker du på **F5** för att felsöka filen.

## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorials/spellcheck.md)

- [Vad är API för stavningskontroll i Bing?](../overview.md)
- [API för stavningskontroll i Bing v7-referens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)