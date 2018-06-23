---
title: Ruby Snabbstartsguide för Azure kognitiva-tjänster, Bing enheten Sök API | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Bing enheten Sök API i kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 0b47430886e70421e51438b56decc9b86d88d0fb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354837"
---
# <a name="quickstart-for-microsoft-bing-entity-search-api-with-ruby"></a>Snabbstart för Microsoft Bing enheten sökning API med Ruby 
<a name="HOLTop"></a>

Den här artikeln visar hur du använder den [Bing enheten Sök](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) API med Ruby.

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) eller senare för att köra den här koden.

Du måste ha en [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **Bing enheten Sök API**. Den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) är tillräcklig för den här snabbstarten. Du måste åtkomstnyckel som tillhandahållits när du aktiverar din kostnadsfria utvärderingsversion eller du kan använda en betald prenumeration nyckel från instrumentpanelen i Azure.

## <a name="search-entities"></a>Sök enheter

Följ dessa steg om du vill köra det här programmet.

1. Skapa ett nytt Ruby-projekt i din favorit IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig snabbtangent för din prenumeration.
4. Kör programmet.

```ruby
require 'net/https'
require 'cgi'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsoft.com'
path = '/bing/v7.0/entities'

mkt = 'en-US'
query = 'italian restaurants near me'

params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
uri = URI (host + path + params)

request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = subscriptionKey

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

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
        "url": "http://www.princi.com/",
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

[Överst på sidan](#HOLTop)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bing enheten Sök kursen](../tutorial-bing-entities-search-single-page-app.md)
> [Bing enheten Sök översikt](../search-the-web.md )
> [API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
