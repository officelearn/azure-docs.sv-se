---
title: 'Quickstart: Check spelling with the REST API and C# - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Kom igång med REST API för stavningskontroll i Bing för att kontrollera stavning och grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: e51c1220e120d157ea4a413b95a7beb20c950518
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378918"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Quickstart: Check spelling with the Bing Spell Check REST API and C#

Använd den här snabbstarten för att göra ditt första anrop till REST API för stavningskontroll i Bing. Det här enkla C#-programmet skickar en begäran till API:et och returnerar en lista över föreslagna korrigeringar. Även om det här programmet är skrivet i C#, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden för det här programmet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Krav

* Any edition of [Visual Studio 2017 or later](https://www.visualstudio.com/downloads/).
* To install `Newtonsoft.Json` as a NuGet package in Visual studio:
    1. In **Solution Explorer**, right-click the Solution file.
    1. Select **Manage NuGet Packages for Solution**.
    1. Search for `Newtonsoft.Json` and install the package.
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Create a new console solution named `SpellCheckSample` in Visual Studio. Lägg sedan till följande namnrymder i huvudkodfilen.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Skapa variabler för API-slutpunkten, prenumerationsnyckeln och den text som ska stavningskontrolleras.

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

3. Skapa en variabel för sökparametrarna. Append your market code after `mkt=`. The market code is the country you make the request from. Also, append your spell-check mode after `&mode=`. Mode is either `proof` (catches most spelling/grammar errors) or `spell` (catches most spelling but not as many grammar errors).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Skapa och skicka en begäran om stavningskontroll

1. Skapa en asynkron funktion som heter `SpellCheck()` för att skicka en begäran till API:et. Skapa en ny `HttpClient` och lägg till prenumerationsnyckeln i `Ocp-Apim-Subscription-Key`-rubriken. Utför sedan följande steg i funktionen.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Create the URI for your request by appending your host, path, and parameters.
    
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

I Main-funktionen i projektet anropar du `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

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
- [API-referens för stavningskontroll i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
