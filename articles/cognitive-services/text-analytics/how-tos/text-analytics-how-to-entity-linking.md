---
title: Använda entitetslänkning med API för textanalys
titleSuffix: Azure Cognitive Services
description: Lär dig att identifiera och lösa entiteter med hjälp av den REST API för textanalys.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/12/2018
ms.author: ashmaka
ms.openlocfilehash: ad2168806f9ddd124faf66cdb5a0f51ed13dfadc
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604747"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Så här identifierar du länkade entiteter i Text Analytics (förhandsversion)

Den [API för Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) tar ostrukturerad text och för varje JSON-dokument, returnerar du en lista över skiljas åt entiteter med länkar till mer information på webben (Wikipedia och Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Entity Linking vs. Igenkänning av namngiven enhet

I bearbetning av naturligt språk, kan du enkelt ihop begreppen entitetslänkning och igenkänning av namngivna entiteter (NER). I förhandsversionen av Text Analytics `entities` slutpunkt, bara för entitetslänkning stöds.

Entitetslänkning är möjligheten att identifiera och disambiguate identiteten för en entitet som påträffats i texten (t.ex. avgöra om ”Mars” används som arrangemang eller latinska krigsguden). Den här processen kräver förekomsten av en knowledge base som känns igen entiteter är länkade – Wikipedia används som kunskapsbas för den `entities` endpoint textanalys.

### <a name="language-support"></a>Stöd för språk

Med hjälp av entitetslänkning på olika språk kräver en motsvarande kunskapsbas på varje språk. För entitetslänkning i Text Analytics, det innebär att alla språk som stöds av den `entities` endpoint länkar till motsvarande Wikipedia-Kristi på det språket. Eftersom storleken på korpus varierar mellan olika språk, förväntas varierar även funktionens återkallande för entitetslänkning.


## <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: id-, text-, språk

Språk som stöds för närvarande finns i [listan](../text-analytics-supported-languages.md).

Dokumentstorlek måste vara under 5 000 tecken per dokument och du kan ha upp till 1 000 objekt (ID) per samling. Samlingen har skickats i brödtexten i begäran. I följande exempel är en illustration av innehåll som du kan skicka till länkramverk entitetsänden.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Steg 1: Strukturera begäran

Information om begäran-definition finns i [hur du anropar API för textanalys](text-analytics-how-to-call-api.md). Följande punkter har omarbetats för att underlätta:

+ Skapa en **POST** begäran. Läs API-dokumentationen för denna begäran: [API för Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Ange HTTP-slutpunkt för extrahering av diskussionsämne. Det måste innehålla den `/entities` resursen: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Ange en rubrik för begäran att inkludera åtkomstnyckeln för Text Analytics-åtgärder. Mer information finns i [hur du hittar slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange JSON-dokument samlingen som du har förberett för den här analysen i begärandetexten,

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna den **API testkonsolen** i den [dokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analysen utförs vid mottagning av begäran. Tjänsten accepterar upp till 100 begäranden per minut. Varje begäran kan vara högst 1 MB.

Kom ihåg att tjänsten är tillståndslösa. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.

## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Alla POST-förfrågningar returnerar en JSON-formaterad svar med ID: N och identifierade egenskaper.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör att du kan sortera, söka och manipulera data.

Ett exempel på utdata för entitetslänkning visas nästa:

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

När det är tillgängligt, innehåller svaret Wikipedia-ID, Wikipedias URL och Bing-ID för varje identifierad entitet. Dessa kan användas för att förbättra ditt program med information om den länkade entiteten.


## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för entitetslänkning med textanalys i Cognitive Services. Sammanfattningsvis:

+ [API för Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) är tillgänglig för valda språken.
+ JSON-dokument i begärandetexten innehålla ett id, text och språk-kod.
+ POST-begäran är att en `/entities` slutpunkten, med hjälp av en personligt anpassad [åtkomst till nyckeln och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för prenumerationen.
+ Svarsutdata som består av länkade entiteter (inklusive säker poäng, förskjutningar och webblänkar, för varje dokument ID) kan användas i alla program

## <a name="see-also"></a>Se också 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics produktsida](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API för textanalys](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
