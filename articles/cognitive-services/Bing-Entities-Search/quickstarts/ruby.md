---
title: 'Snabbstart: Skicka en sökbegäran till REST API för Entitetssökning i Bing med Ruby'
titlesuffix: Azure Cognitive Services
description: Använd den här snabbstarten om du vill skicka en begäran till REST API för entitetssökning i Bing med hjälp av Ruby och få ett JSON-svar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 35659a77f80bd3b3d105b9ea91513784dbca01c4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539011"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Snabbstart för API för entitetssökning i Bing med Ruby

Använd den här snabbstarten för att göra ditt första anrop till API för entitetssökning i Bing och visa JSON-svaret. Det här enkla Ruby-programmet skickar en nyhetssökfråga till API:et och visar svaret. Källkoden till det här programmet finns [på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb).

Även om det här programmet är skrivet i Ruby, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) eller senare.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. I dina favorit-IDE eller kodredigerare skapar du en Ruby-nyhetsfil och importerar följande paket.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Skapa variabler för API-slutpunkten, din webbadress för nyhetssökning och en sökfråga.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Formatera och göra en API-begäran

1. Skapa parametersträngen för din begäran genom att lägga till marknadsvariabeln till parametern `?mkt=`. Koda frågan och bifoga den i parametern `&q=`. Kombinera din API-värd, sökväg och parametrarna för din begäran och omvandla dem till ett URI-objekt.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. Använd variablerna i det sista steget för att skapa begäran. Lägg till din prenumerationsnyckel i `Ocp-Apim-Subscription-Key`-huvudet.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. Skicka begäran och skriva ut svaret

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
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

* [Vad är API:et för entitetssökning i Bing?](../search-the-web.md)
* [Referens för API för entitetsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
