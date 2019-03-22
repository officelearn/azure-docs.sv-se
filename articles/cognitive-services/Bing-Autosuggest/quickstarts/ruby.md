---
title: 'Snabbstart: Föreslå sökfrågor med REST API för automatiska förslag i Bing och Ruby'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med API:et för automatiska förslag i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: c7ba0fd34c789735cd92c25a728aec346dc88fcc
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57009757"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-ruby"></a>Snabbstart: Föreslå sökfrågor med REST API för automatiska förslag i Bing och Ruby

Använd den här snabbstarten ska börja göra anrop till API för automatiska förslag i Bing och hämta JSON-svar. Den här enkla Ruby-programmet skickar en partiell sökfråga till API: et och returnerar förslag på sökningar. Även om det här programmet är skrivet i Ruby, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.


## <a name="prerequisites"></a>Förutsättningar

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) eller senare.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny Ruby-fil i din favorit-IDE eller redigerare. Lägg till följande krav:

    ```ruby
    require 'net/https'
    require 'uri'
    require 'json'
    ```

2. Skapa variabler för dina API-värden och sökväg, [marknaden kod](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#market-codes), delvis sökfråga.

    ```ruby
    subscriptionKey = 'enter your key here'
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/Suggestions'
    mkt = 'en-US'
    query = 'sail'
    ```

3. Skapa en parametrar sträng genom att lägga till marknaden koden så att den `?mkt=` parametern och lägga till frågan i `&q=` parametern. Skapa sedan din begäran URI genom att kombinera API-värden, sökväg och strängen som parametrar.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + query
    uri = URI (host + path + params)
    ```

## <a name="create-and-send-an-api-request"></a>Skapa och skicka en API-begäran

1. Skapa en begäran med din URI och Lägg till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik.
    
    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

2. Skicka begäran och lagra svaren.
    
    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end
    ```

3. Skriva ut JSON-svar.
    
    ```ruby
    puts JSON::pretty_generate (JSON (response.body))
    ```

## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorials/autosuggest.md)

## <a name="see-also"></a>Se också

- [Vad är automatiska förslag i Bing?](../get-suggested-search-terms.md)
- [Referens för API v7 för automatiska förslag i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference)
