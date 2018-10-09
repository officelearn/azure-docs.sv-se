---
title: Vad är Project Answer Search?
titlesuffix: Azure Cognitive Services
description: Introduktion till Svarssökning projekt.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 87fe7b008e3e7c6cd8d1a9a870c0fb8ce2f6a7cd
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868266"
---
# <a name="what-is-project-answer-search"></a>Vad är Project Answer Search?
Projektet svar Search API använder Bing v7 slutpunkten för att få svar på interrogative frågor. En fråga som ”hur är omkrets jorden”? Returnerar ett svar med faktauppgifter.  En fråga om en person, plats eller sak returnerar information om entiteten identifieras av frågan. Dessa scenarier kan vara användbart i program, till exempel konversationsanpassade robotar, messaging appar, läsare, osv.  

Svar som beror på scenariot query-frågor returnerar: webbsidor är alltid returneras, medan [fakta](fact-queries.md) och/eller [entiteter](entity-queries.md) returneras om det behövs.

## <a name="endpoint"></a>Slutpunkt
Skicka en begäran till svar Search API-slutpunkt för att få svar på en fråga eller information om en person, plats eller sak. Använd rubriker och URL-parametrar för olika specifikationer.  Inkludera *Ocp-Apim-Subscription-Key* huvud med en giltig token.  Marknaden-parametern är obligatorisk. Endast `en-us` marknaden stöds för närvarande.

Följande fråga hämtar svar på frågan: ”Vad är omkrets jorden”?

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

URL-parameter `q=` krävs för att ange objekt av search.

## <a name="response-object"></a>-Svarsobjekt

Svaret innehåller HTTP-rubriker, webbsidor, fakta och entiteter.

````
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
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Earth"
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
              "url": "http://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
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

````

## <a name="terms-of-use"></a>Användningsvillkor
Svarssökning för projektet och projekt Video trender är föremål för de [Bing Search Använd och visa krav](use-display-requirements.md).

Du eller en tredje part för din räkning kan inte använda, behålla, lagra, cache, delar, eller distribuera alla data från API: et för URL-förhandsgranskning för testning, utveckling, utbildning, distribuerar eller gör tillgänglig någon icke-Microsoft-tjänst eller funktion. 

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Data attribution  

Projektet Svarssökning svar innehåller information som ägs av tredje part. Du ansvarar för att se till att din användning är lämpligt, till exempel uppfyller en creative commons-licens användarupplevelsen kan förlita sig på.  
  
Om ett svar eller ett resultat som innehåller den `contractualRules`, `attributions`, eller `provider` fält, du måste attributet data. Om svaret inte innehåller några av de här fälten, krävs ingen information. Om svaret innehåller den `contractualRules` fält och `attributions` och/eller `provider` fält, måste du använda avtalsenliga reglerna för att attributet data.  
  
I följande exempel visas en entitet som innehåller en MediaAttribution avtalsenliga regel och en avbildning som innehåller en `provider` fält. MediaAttribution regeln identifierar bilden som mål för regeln, så att du vill ignorera en bilds `provider` fältet och i stället använda MediaAttribution regeln för att ge information.  
  
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
  
Om en avtalsenliga regel innehåller den `targetPropertyName` fältet regeln gäller endast för fältet riktade. I annat fall regeln gäller för det överordnade objektet som innehåller den `contractualRules` fält.  
  
  
I följande exempel visas den `LinkAttribution` regeln innehåller den `targetPropertyName` fältet har regeln gäller för den `description` fält. Du måste inkludera en rad direkt efter den aktuella data som innehåller en hyperlänk till leverantörens webbplats för regler som gäller för specifika fält. Till exempel för att attributet beskrivning, inkludera en rad omedelbart efter en beskrivande text som innehåller en hyperlänk till data på leverantörens webbplats, i det här fallet att skapa en länk till en.wikipedia.org.  
  
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

### <a name="license-attribution"></a>Licens Attribution  

Om listan över regler som avtalsenliga innehåller en [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) regeln, måste du visa meddelandet på raden direkt efter det innehåll som licensen gäller för. Den `LicenseAttribution` regel använder den `targetPropertyName` fält för att identifiera den egenskap som licensen gäller för.  
  
Följande visar ett exempel som innehåller en `LicenseAttribution` regel.  
  
![Licens attribution](./media/licenseattribution.png)  
  
Licens-meddelande som du visar måste innehålla en hyperlänk till webbplatsen som innehåller information om licensen. Normalt kan göra du namnet på licensen som en hyperlänk. Om meddelandet är till exempel **Text under kopia av SA licens** och kopia av SA är namnet på licensen, du kan göra en kopia av SA en hyperlänk.  
  
### <a name="link-and-text-attribution"></a>Länken och Text Attribution  

Den [LinkAttribution](reference.md#linkattribution) och [TextAttribution](reference.md#textattribution) regler används vanligtvis för att identifiera leverantören av data. Den `targetPropertyName` fältet identifierar fältet som regeln gäller för.  
  
Inkludera en rad som omedelbart efter det innehåll som gäller för uppgift till (till exempel riktade fältet) för att attributet providers. Raden ska stå tydligt för att visa att providers är källan för data. Till exempel ”Data från: en.wikipedia.org”. För `LinkAttribution` regler, måste du skapa en hyperlänk till leverantörens webbplats.  
  
Följande visar ett exempel som innehåller `LinkAttribution` och `TextAttribution` regler.  
  
![Länken text attribution](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Media Attribution  

Om entiteten tas en bild och du visar det, måste du ange en klicka igenom länk till leverantörens webbplats. Om entiteten innehåller en [MediaAttribution](reference.md#mediaattribution) regel, använder regelns URL för att skapa klicka igenom länk. Annars kan du använda URL: en som ingår i en bilds `provider` fält som du vill skapa klicka igenom länk.  
  
Följande visar ett exempel som innehåller en bild `provider` fält och avtalsenliga regler. Eftersom exemplet innehåller avtalsenliga regeln, ignoreras avbildningens `provider` och Använd den `MediaAttribution` regeln.  
  
![Media attribution](./media/mediaattribution.png)  

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](c-sharp-quickstart.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för noden](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)
