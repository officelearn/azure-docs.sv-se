---
title: 'Snabb start: utföra en Webbs ökning med Node.js-Webbsökning i Bing REST API'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten om du vill skicka begäranden till REST-API:et för webbsökning i Bing med hjälp av Node.js och få ett JSON-svar
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: d0a57a2d7d6a7898ae530108b8aabc2f31c97df6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076613"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-nodejs"></a>Snabb start: söka på webben med hjälp av Webbsökning i Bing REST API och Node.js

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Använd den här snabb starten för att göra ditt första anrop till API för webbsökning i Bing. Det här Node.js programmet skickar en Sök förfrågan till API: et och visar JSON-svaret. Även om det här programmet är skrivet i Java Script är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

Här följer några saker som du behöver innan du kör den här snabbstarten:

* [Node.js 6](https://nodejs.org/en/download/) eller senare
* En prenumerationsnyckel

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-declare-required-modules"></a>Skapa ett projekt och deklarera nödvändiga moduler

Skapa ett nytt Node.js-projekt i valfri IDE eller redigeringsprogram. Kopiera sedan följande kodfragment till projektet i en fil med namnet search.js:

```javascript
// Use this simple app to query the Bing Web Search API and get a JSON response.
// Usage: node search.js "your query".
const https = require('https')
```

## <a name="set-the-subscription-key"></a>Ange prenumerationsnyckeln

Det här kodfragmentet använder `AZURE_SUBSCRIPTION_KEY` miljövariabeln för att lagra din prenumerations nyckel, vilket är en bra idé att förhindra oavsiktlig exponering av nycklar när du distribuerar kod.

Om du inte känner till användningen av miljövariabler, eller om du vill köra den här appen så snabbt som möjligt, ersätter du `process.env['AZURE_SUBSCRIPTION_KEY']` med din prenumerations nyckel uppsättning som en sträng.

```javascript
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('AZURE_SUBSCRIPTION_KEY is not set.')
}
```

## <a name="create-a-function-to-make-the-request"></a>Skapa funktionen som ska göra begäran

Den här funktionen gör en säker GET-begäran och sparar Sök frågan som en frågeparameter i sökvägen. 

1. För `hostname` värdet kan du använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.  

2. Används `encodeURIComponent` för att undanta ogiltiga tecken. Prenumerations nyckeln skickas i en rubrik. 

3. Återanropet tar emot ett [svar](https://nodejs.org/dist/latest-v10.x/docs/api/http.html#http_class_http_serverresponse) som prenumererar på händelsen `data` för att sammanställa JSON-texten, händelsen `error` för att logga eventuella problem och händelsen `end` för att veta när meddelandet ska betraktas som färdigt. 

4. När appen har slutförts skrivs relevanta rubriker och meddelande text ut. Du kan justera färgerna och ange djupet som passar dina inställningar. Ett djup i `1` ger en bra sammanfattning av svaret.

```javascript
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
```

## <a name="get-the-query"></a>Hämta frågan

Låt oss titta på programmets argument och leta efter frågan. Det första argumentet är sökvägen till noden, det andra är vårt fil namn och den tredje är din fråga. Om det inte finns någon fråga används standardfrågan ”Microsoft Cognitive Services”.

```javascript
const query = process.argv[2] || 'Microsoft Cognitive Services'
```

## <a name="make-a-request-and-print-the-response"></a>Göra en begäran och skriva ut svaret

Nu när allt har definierats kan vi anropa vår funktion.

```javascript
bingWebSearch(query)
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det sista steget är att köra koden med kommandot: `node search.js "<your query>"` .

Om du vill jämföra din kod med vår finns det fullständiga programmet här:

```javascript
const https = require('https')
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('Missing the AZURE_SUBSCRIPTION_KEY environment variable')
}
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
const query = process.argv[2] || 'Microsoft Cognitive Services'
bingWebSearch(query)
```

## <a name="example-json-response"></a>Exempel på JSON-svar

Svar från API för webbsökning i Bing returneras som JSON. Det här exempelsvaret har trunkerats för att visa ett enskilt resultat.

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face service from Microsoft Azure. ... Cognitive Services; Face service;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Själv studie kurs om API för webbsökning i Bing enkel sida](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
