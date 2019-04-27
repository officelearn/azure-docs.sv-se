---
title: Vad är Project Answer Search?
titlesuffix: Azure Cognitive Services
description: Introduktion till Project Answer Search.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: ac1717a8e8a08fcfedc3bc21bb0f03b3e3ca2511
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721869"
---
# <a name="what-is-project-answer-search"></a>Vad är Project Answer Search?
Project Answer Search API använder Bing v7-slutpunkten för att hämta svar på interrogativa frågor. En fråga som ”What is the circumference of the earth?” (Hur stor är jordens omkrets?) returnerar ett svar med faktauppgifter.  En fråga om en person, plats eller sak returnerar information om den entitet som identifieras av frågan. Dessa scenarier kan vara användbara i tillämpningar som konversationsrobotar, meddelandeappar, läsare osv.  

Frågor returnerar svar som beror på frågescenariot: webbplatser returneras alltid medan [fakta](fact-queries.md) och/eller [entiteter](entity-queries.md) returneras om de är relevanta.

## <a name="endpoint"></a>Slutpunkt
För att hämta svar på en fråga eller information om en person skickar du en begäran till Answer Search API-slutpunkten. Använd sidhuvudena och URL-parametrarna för olika specifikationer.  Inkludera sidhuvudet *Ocp-Apim-Subscription-Key* med en giltig token.  Marknadsparametern krävs. För närvarande stöds endast marknaden `en-us`.

Följande fråga hämtar svar på frågan: ”Vad är jordens omkrets”?

GET:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

URL-parametern `q=` krävs för att ange objektet för sökningen.

## <a name="response-object"></a>Svarsobjekt

Svaret innehåller HTTP-sidhuvuden, webbplatser, fakta och/eller entiteter.

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "https://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>Användningsvillkor
Project Answer Search och Project Video Trends är föremål för [kraven för användning och visning för Bing-sökning](use-display-requirements.md).

Du eller en tredje part för din räkning får inte använda, behålla, lagra, cachelagra, dela eller distribuera data från API:et för URL-förhandsgranskning i syfte att testa, utveckla, träna, distribuera eller tillgängliggöra tjänster eller funktioner som inte kommer från Microsoft. 

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Dataattribution  

Project Answer Search-svar innehåller information som ägs av tredje part. Du ansvarar för att se till att din användning är lämplig, till exempel genom att följa gällande Creative Commons-licenser som din användarfunktion är beroende av.  
  
Om ett svar eller resultat innehåller fälten `contractualRules`, `attributions` eller `provider` måste du attributera data. Om svaret inte innehåller några av de här fälten krävs ingen attribution. Om svaret innehåller fältet `contractualRules` och fälten `attributions` och/eller `provider` måste du använda kontraktsreglerna för att attributera data.  
  
I följande exempel visas en entitet som innehåller en MediaAttribution-kontraktsregel och en bild som innehåller ett `provider`-fält. MediaAttribution-regeln identifierar bilden som målet för regeln, så du ignorerar bildens `provider`-fält och använder i stället MediaAttribution-regeln för att ge attribution.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Om en kontraktsregel innehåller fältet `targetPropertyName` gäller regeln endast för målfältet. Annars gäller regeln för det överordnade objekt som innehåller fältet `contractualRules`.  
  
  
I följande exempel innehåller regeln `LinkAttribution` fältet `targetPropertyName`, så regeln gäller för fältet `description`. För regler som gäller för specifika fält måste du inkludera en rad som följer omedelbart efter måldata som innehåller en hyperlänk till källans webbplats. Exempel: för att attributera beskrivningen inkluderar du en rad omedelbart efter den beskrivande texten som innehåller en hyperlänk till data på källans webbplats. I det här fallet skapar du en länk till en.wikipedia.org.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Licensattribution  

Om listan över kontraktsregler innehåller en [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution)-regel måste du visa meddelandet på raden direkt efter det innehåll som licensen gäller för. `LicenseAttribution`-regeln använder fältet `targetPropertyName` för att identifiera den egenskap som licensen gäller för.  
  
Följande visar ett exempel som innehåller en `LicenseAttribution`-regel.  
  
![Licensattribution](./media/licenseattribution.png)  
  
Det licensmeddelande som du visar måste innehålla en hyperlänk till den webbplats som innehåller information om licensen. Normalt gör du namnet på licensen till en hyperlänk. Exempel: om meddelandet är **Text under CC-AV-SA-licensen** och CC-AV-SA är namnet på licensen, gör du CC-AV-SA till en hyperlänk.  
  
### <a name="link-and-text-attribution"></a>Länk- och textattribution  

Reglerna [LinkAttribution](reference.md#linkattribution) och [TextAttribution](reference.md#textattribution) används vanligtvis för att identifiera källan till data. Fältet `targetPropertyName` identifierar det fält som regeln gäller för.  
  
För att attributera källorna inkluderar du en rad omedelbart efter det innehåll som attributionerna gäller för (till exempel målfältet). Raden ska vara tydligt märkt för att visa källorna till data. Ett exempel kan vara ”Data från: en.wikipedia.org”. För `LinkAttribution`-regler måste du skapa en hyperlänk till källans webbplats.  
  
Följande visar ett exempel som innehåller `LinkAttribution`- och `TextAttribution`-regler.  
  
![Länktextattribution](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Medieattribution  

Om entiteten innehåller en bild och du visar den måste du tillhandahålla en klickbar länk till källans webbplats. Om entiteten innehåller en [MediaAttribution](reference.md#mediaattribution)-regel använder du regelns URL för att skapa den klickbara länken. Annars använder du den URL som ingår i bildens `provider`-fält för att skapa den klickbara länken.  
  
Följande visar ett exempel som innehåller en bilds `provider`-fält och kontraktsregler. Eftersom exemplet innehåller kontraktsregeln ignorerar du bildens `provider`-fält och tillämpar `MediaAttribution`-regeln.  
  
![Medieattribution](./media/mediaattribution.png)  

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](c-sharp-quickstart.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för Node](node-quickstart.md)
- [Snabbstart för Python](python-quickstart.md)
