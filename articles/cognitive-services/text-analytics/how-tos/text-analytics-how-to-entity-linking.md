---
title: Anvisningar använda entiteten länkar i Text Analytics REST-API (Microsoft kognitiva tjänster i Azure) | Microsoft Docs
description: Lär dig att identifiera och lösa enheter med hjälp av REST-API Text Analytics i kognitiva Microsoft-tjänster i Azure i den här genomgången självstudiekursen.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352713"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Så här identifierar du länkade entiteter i textanalys (förhandsgranskning)

Den [entitet länka API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) tar Ostrukturerade text och för varje JSON-dokument returnerar en lista över skiljas åt entiteter med länkar till mer information på webben (Wikipedia och Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Entiteten länka vs. Igenkänning av namngiven enhet

Vid bearbetning av naturligt språk kan begreppen entitet länkning och namngiven enhet recognition (NER) enkelt blandas ihop. I av förhandsversionen av Text Analytics `entities` slutpunkt, bara entitetstyper länkning stöds.

Enheten länkning har möjlighet att identifiera och undvika tvetydigheten identiteten för en entitet som påträffats i texten (t.ex. avgöra om ”Mars” används som planeten eller latinska krigsguden). Den här processen kräver förekomsten av en knowledge base som identifieras entiteter är länkade – Wikipedia används som kunskapsbas för den `entities` endpoint Text Analytics.

### <a name="language-support"></a>Språkstöd

Entitet som länkar i olika språk kräver att en motsvarande knowledge base på varje språk. För entiteten i textanalys, innebär detta att varje språk som stöds av den `entities` slutpunkten ska kopplas till den motsvarande Wikipedia Kristi på det språket. Eftersom storleken på corpora varierar mellan olika språk, förväntas det att entiteten länka funktioners återkallning varierar.


## <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: id, text, språk

Språk som stöds för närvarande finns [listan](../text-analytics-supported-languages.md).

Storlek måste vara under 5 000 tecken per dokument och du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i meddelandetexten i begäran. I följande exempel är en illustration av innehåll som du kan skicka till entiteten länkande slutet.

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
    
## <a name="step-1-structure-the-request"></a>Steg 1: Struktur begäran

Information om begäran definition finns i [hur du anropar API: et för Text Analytics](text-analytics-how-to-call-api.md). Följande punkter räknas av praktiska skäl:

+ Skapa en **POST** begäran. Granska API-dokumentationen för denna begäran: [entitet länka API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Ange HTTP-slutpunkten för viktiga frasen extrahering. Det måste innehålla den `/entities` resursen: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Ange ett huvud med snabbtangenten för Text Analytics-åtgärder. Mer information finns i [hur du hittar slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange samlingen JSON-dokument du förberedd för den här analysen i frågans brödtext

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna den **API-testet konsolen** i den [dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) struktur en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analys utförs vid mottagning av begäran. Tjänsten accepterar upp till 100 begäranden per minut. Varje begäran kan vara upp till 1 MB.

Kom ihåg att tjänsten är tillståndslösa. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.

## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Alla POST-förfrågningar returnera en JSON-formaterad svar med ID: N och egenskaper identifierades.

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör att du kan sortera, söka och manipulera data.

Ett exempel på utdata för entiteten länka visas nästa:

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

När det är tillgängligt, innehåller svaret Wikipedia ID, Wikipedia URL och Bing-ID för varje identifierad entitet. Dessa kan användas för att ytterligare förbättra ditt program med information om länkade entiteten.


## <a name="summary"></a>Sammanfattning

I den här artikeln får du har lärt dig begrepp och arbetsflöde för entiteten länkning med hjälp av Text Analytics i kognitiva Services. Sammanfattningsvis:

+ [API för länkning av entiteten](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) är tillgänglig för markerade språk.
+ JSON-dokument i begärandetexten innehåller ett id, text och språk-kod.
+ POST-begäran är att en `/entities` slutpunkten, med hjälp av en anpassad [åtkomst till nyckeln och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för prenumerationen.
+ Svarsutdata som består av länkade entiteter (inklusive förtroende poäng, förskjutningar och webblänkar, för varje dokument ID) kan användas i alla program

## <a name="see-also"></a>Se också 

 [Översikt över text Analytics](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics produktsidan](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
