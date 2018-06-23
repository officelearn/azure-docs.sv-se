---
title: Projektet svaret Sök översikt – kognitiva Microsoft-tjänster | Microsoft Docs
description: Introduktion till projektet svaret sökningen.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: d87cf1390970d2c815b94bcaee7e07c19bc03cce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354117"
---
# <a name="what-is-project-answer-search"></a>Vad är Project svaret sökningen?
Projektet svaret Sök-API använder Bing v7 slutpunkten för att få svar på interrogative frågor. En fråga som ”hur är omkrets jorden”? Returnerar ett svar med fakta.  En fråga för en person, en plats eller en sak returnerar information om enheten som identifieras av frågan. Dessa scenarier kan vara användbart i program som vardagliga samtalsuttryck robotar, messaging appar, läsare osv.  

Frågor som returnerar svar som är beroende av scenariot för frågan: webbsidor är alltid returnerade, medan [fakta](fact-queries.md) och/eller [entiteter](entity-queries.md) returneras om det behövs.

## <a name="endpoint"></a>Slutpunkt
Skicka en begäran till svaret Sök API-slutpunkt för att få svar på en fråga eller information om en person, en plats eller en sak. Använd rubriker och URL-parametrarna för olika specifikationer.  Inkludera *Ocp-Apim-prenumeration-nyckeln* huvud med en giltig token.  Parametern marknaden krävs. Endast `en-us` marknaden stöds för närvarande.

Följande fråga hämtar svar på frågan: ”Vad är omkrets jorden”?

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

URL-parametern `q=` krävs för att ange objekt av sökningen.

## <a name="response-object"></a>Objektet Response

Svaret innehåller HTTP-huvuden, webbsidor, fakta och/eller enheter.

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
Projektet svaret Sök och projektet Video trender är föremål för den [användas för Bing Search och visa krav](use-display-requirements.md).

Du eller någon tredje part å dina vägnar kan inte använda, behålla, lagra, cache, dela, eller distribuera alla data från API: et för URL-Preview för testning, utveckling, utbildning, distribuera eller gör alla icke-Microsoft-tjänster eller funktion. 

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Fördelning av data  

Projektet svaret Sök svar innehåller information som ägs av tredje part. Du är ansvarig för att se till att du använder är lämpligt, till exempel genom att följa kreativa commons licens användarupplevelsen kan förlita sig på.  
  
Om ett svar eller resultatet innehåller den `contractualRules`, `attributions`, eller `provider` fält, du måste attributet data. Om svaret inte innehåller några av dessa fält krävs ingen information. Om svaret innehåller den `contractualRules` fält och `attributions` och/eller `provider` fält, måste du använda avtal reglerna för att attributet data.  
  
I följande exempel visas en entitet som innehåller en MediaAttribution avtal regel och en avbildning som omfattar en `provider` fältet. MediaAttribution regeln identifierar bilden som mål för regeln, så du kan ignorera bilden `provider` fältet och Använd i stället MediaAttribution regel för att ange tillskrivningar.  
  
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
  
Om en regel för avtal innehåller den `targetPropertyName` fältet regeln gäller endast för det aktuella fältet. Annars regeln gäller för det överordnade objektet som innehåller den `contractualRules` fältet.  
  
  
I följande exempel visas den `LinkAttribution` regeln innehåller den `targetPropertyName` fältet, så att regeln gäller för den `description` fältet. För regler som gäller för specifika fält, måste du inkludera en rad direkt efter måldata som innehåller en hyperlänk till leverantörens webbplats. Till exempel för att attributet beskrivningen innehåller en rad omedelbart efter en beskrivande text som innehåller en hyperlänk till data på leverantörens webbplats i det här fallet att skapa en länk till en.wikipedia.org.  
  
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

### <a name="license-attribution"></a>Licens-information  

Om listan över regler som avtal innehåller en [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) regeln, måste du visa meddelandet på raden omedelbart efter det innehåll som licensvillkoren gäller för. Den `LicenseAttribution` regel använder den `targetPropertyName` fältet för att identifiera den egenskap som licensvillkoren gäller för.  
  
Följande är ett exempel som innehåller en `LicenseAttribution` regel.  
  
![Licens-information](./media/licenseattribution.png)  
  
Licens-meddelande som visas måste innehålla en hyperlänk till den webbplats som innehåller information om licensen. Normalt du namnet på licensen som en hyperlänk. Om meddelandet är till exempel **Text under kopia av SA licens** och kopia av SA är namnet på licensen, du kan göra en kopia av SA en hyperlänk.  
  
### <a name="link-and-text-attribution"></a>Länken och tillskrivningar Text  

Den [LinkAttribution](reference.md#linkattribution) och [TextAttribution](reference.md#textattribution) regler används vanligtvis för att identifiera leverantören av data. Den `targetPropertyName` fältet identifierar det fält som regeln gäller för.  
  
Innehåller en rad direkt efter det innehåll som gäller för uppgift till (till exempel riktade fältet) för att attributet providers. Raden ska stå klart för att visa att providers är källan för data. Till exempel ”Data från: en.wikipedia.org”. För `LinkAttribution` regler, måste du skapa en hyperlänk till leverantörens webbplats.  
  
Följande är ett exempel som innehåller `LinkAttribution` och `TextAttribution` regler.  
  
![Länken text information](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Media-information  

Om entiteten innehåller en avbildning och du visa det, måste du ange en klicka igenom länk till leverantörens webbplats. Om entiteten innehåller en [MediaAttribution](reference.md#mediaattribution) regel, använda regelns URL för att skapa länken klicka igenom. Annars använder den URL som ingår i bildens `provider` fältet för att skapa länken klicka igenom.  
  
Följande är ett exempel som innehåller en bild `provider` fältet och avtal regler. Eftersom exemplet inkluderar avtal regeln kan ignoreras bildens `provider` och Använd den `MediaAttribution` regeln.  
  
![Media-information](./media/mediaattribution.png)  

## <a name="next-steps"></a>Nästa steg
- [C#-Snabbstart](c-sharp-quickstart.md)
- [Java-Snabbstart](java-quickstart.md)
- [Noden Snabbstart](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)
