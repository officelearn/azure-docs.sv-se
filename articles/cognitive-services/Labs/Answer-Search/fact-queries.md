---
title: 'Snabbstart: Project Answer Search-faktafråga'
titlesuffix: Azure Cognitive Services
description: Fråga efter fakta med Project Answer Search
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: 7e8a793362e51a05a73c0b42346e2e8fafb3f44d
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469409"
---
# <a name="quickstart-query-for-facts"></a>Snabbstart: Fråga efter fakta

Om du vill fråga efter fakta, till exempel ett datum eller identifierbar information, kan svaret innehålla `facts`-svar. Faktasvar innehåller relevanta resultat som extraheras från stycken i webbdokument.  Dessa frågor returnerar alltid webbsidor, och [fakta](fact-queries.md) och/eller [entiteter](entity-queries.md) är frågeberoende.

Frågor som alla+hjärtans+dag+2016 eller när+är+ramadan är exempel på datumrelaterade frågor. Om Bing avgör att frågan är datumrelaterad innehåller svaret ett `facts`-svar. 

Följande exempel är ett datumrelaterat `facts`-svar. 

**Fråga:**
````
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=valentines+2016

````

**Svar:** Fältet `subjectName` innehåller en visningsversion av användarens fråga som du kan använda som en etikett när du visar fakta. Om frågesträngen är alla+hjärtans+dag+2016 kan Bing ändra det till Alla hjärtans dag 2016. Beskrivningsfältet innehåller faktainformationen.

````
{   
    "_type" : "SearchResponse",   
    "queryContext" : {   
        "originalQuery" : "valentines 2016" 
    },   
    "facts" : {   
        "id" : "https:\/\/www.bingapis.com\/api\/v7\/#Facts",   
        "value" : [{   
            "description" : "Valentine's Day was on Sunday, February 14, 2016.",   
            "subjectName" : "Valentine's Day 2016"   
        }]   
    },   
    "rankingResponse" : {   
        "mainline" : {   
            "items" : [{   
                "answerType" : "Facts",   
                "value" : {   
                    "id" : "https:\/\/www.bingapis.com\/api\/v7\/knowledge\/#Facts"                   }   
            }]   
        }   
    }   
}   

````

Frågan ”Varför är himlen blå?” returnerar ett exempel på ett kunskapsrelaterat svar.

**Fråga:**

````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=why+is+the+sky+blue

````

**Svar:** Fältet `value/description` innehåller kunskapen eller informationen som efterfrågas i frågan.

````
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "url": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "spaceplace.nasa.gov/blue-sky/en/",
        "url": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "seeMoreUrl": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "providerDisplayName": "spaceplace.nasa.gov/blue-sky/en/",
        "seeMoreUrl": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "value": [
      {
        "image": {
          "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&q=&id=B632272C4E934D7F0B18790300B2DE34E7676C7A&simid=608045681196075791&iss=eqna",
          "name": "Why is the sky blue in the sky?",
          "thumbnailUrl": "https://www.bing.com/th?id=ODE.858093005&w=140&h=140&c=8&rs=1&qlt=100&pid=3.1",
          "isFamilyFriendly": true
        },
        "description": "When sunlight reaches Earth's atmosphere, it is scattered in all directions by all the gases and particles in the air. Blue is scattered by air molecules more than other colors because it travels as shorter, smaller waves. This is why we see a blue sky most of the time.\n\nCloser to the horizon, the sky fades to a lighter blue or white. The sunlight reaching us from low in the sky has passed through even more air than the sunlight reaching us from overhead. As the sunlight has passed through all this air, the air molecules have scattered and rescattered the blue light many times in many directions. Also, the surface of Earth has reflected and scattered the light. All this scattering mixes the colors together again so we see more white and less blue.",
        "subjectName": "Why is the sky blue in the sky?",
        "primaryData": [
          "The atmosphere scatters more blue light"
        ]
      }
    ]
  },

````

## <a name="tabular-data"></a>Tabelldata
Ibland kan fakta returneras som `_type: StructuredValue/TabularData`. Följande fråga hämtar tabelldata med kontrasterande information om kaffe och te.

````
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=coffee+vs+tea&mkt=en-us 

````
`facts`-resultatet innehåller följande rader och celler:
````
    "value": [
      {
        "subjectName": "Coffee vs. Tea",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "",
            "Coffee",
            "Tea"
          ],
          "rows": [
            {
              "cells": [
                {
                  "text": "Part of plant used"
                },
                {
                  "text": "Bean"
                },
                {
                  "text": "Leaf"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Caffeine Content"
                },
                {
                  "text": "80-185 mg per 8 ounce cup depending upon the brew and the..."
                },
                {
                  "text": "15 - 70 mg per cup"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Types of Consumption"
                },
                {
                  "text": "Drip Coffee"
                },
                {
                  "text": "White Tea"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Additions"
                },
                {
                  "text": "Sugar"
                },
                {
                  "text": "Milk"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "8 more rows",
            "url": "http://www.diffen.com/difference/Coffee_vs_Tea"
          }
        }
      }
    ]
  },

````

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](c-sharp-quickstart.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för Node](node-quickstart.md)
- [Snabbstart för Python](python-quickstart.md)
