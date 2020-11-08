---
title: 'Snabb start: kontrol lera stavningen med REST API och ruby-Stavningskontroll i Bing'
titleSuffix: Azure Cognitive Services
description: Kom igång med Stavningskontroll i Bing REST API och ruby för att kontrol lera stavning och grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 8b347adab20447c542aaee1b7d41476233054824
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366884"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Snabb start: kontrol lera stavningen med Stavningskontroll i Bing REST API och ruby

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Använd den här snabbstarten för att skicka ditt första anrop till REST API för stavningskontroll i Bing med hjälp av Ruby. Det här enkla programmet skickar en begäran till API: et och returnerar en lista med föreslagna korrigeringar. 

Även om det här programmet är skrivet i ruby är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden till det här programmet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Förutsättningar

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) eller senare.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny ruby-fil i din favorit redigerare eller IDE och Lägg till följande krav: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Skapa variabler för din prenumerations nyckel, slut punkts-URI och sökväg. Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs. Skapa parametrarna för begäran:

   1. Tilldela din marknads kod till- `mkt` parametern med `=` operatorn. Marknads koden är koden för landet/regionen som du gör begäran från. 

   1. Lägg till `mode` parametern med `&` operatorn och tilldela sedan läget för stavnings kontroll. Läget kan vara antingen `proof` (fångar de flesta stavfel/grammatikfel) eller `spell` (fångar de flesta stavfel, men inte lika många grammatiska fel). 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Skicka en begäran om stavningskontroll

1. Skapa en URI från din värd-URI, sökväg och parametersträng. Ange att frågan ska innehålla den text som du vill stavnings kontrol lera.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Skapa en begäran med hjälp av den URI som skapats tidigare. Lägg till nyckeln till `Ocp-Apim-Subscription-Key`-rubriken.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Skicka begäran.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Hämta JSON-svaret och skriv ut det till konsolen. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>Kör programmet

Skapa och kör ditt projekt. Om du använder kommando raden använder du följande kommando för att köra programmet:

   ```bash
   ruby <FILE_NAME>.rb
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
- [API för stavningskontroll i Bing v7-referens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)