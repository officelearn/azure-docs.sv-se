---
title: Anvisningar viktiga frasen extrahering i Text Analytics REST-API (Microsoft kognitiva tjänster i Azure) | Microsoft Docs
description: Hur man extraherar viktiga uttryck med hjälp Text Analytics REST API i kognitiva Microsoft-tjänster i Azure i den här genomgången självstudiekursen.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: 78b100e737242fa9f56e50275ef2038d8895349e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352233"
---
# <a name="how-to-extract-key-phrases-in-text-analytics"></a>Hur man extraherar viktiga fraser i textanalys

Den [nyckel frasen extrahering API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) utvärderar Ostrukturerade text och returnerar en lista över viktiga fraser för varje JSON-dokument. 

Den här funktionen är användbart om du behöver att snabbt kunna identifiera de huvudsakliga delarna i en samling av dokument. Till exempel angivna indata som ”mat har delikat och det fanns fantastiska personal” tjänsten returnerar de viktigaste aspekterna prata: ”Mat” och ”fantastiska personal”.

Nyckeln frasen extrahering stöder för närvarande, engelska, tyska, spanska och japanska. Andra språk finns i förhandsgranskningen. Mer information finns i [språk som stöds](../text-analytics-supported-languages.md).

## <a name="preparation"></a>Förberedelse

Viktiga frasen extrahering fungerar bäst när ge större mängder text att arbeta med. Detta är motsatt från sentiment analys, vilken som presterar bäst på mindre block med text. Överväg att omstrukturera indata i enlighet med detta för att få bästa möjliga resultat från båda åtgärder.

Du måste ha JSON-dokument i det här formatet: id, text, språk

Storlek måste vara under 5 000 tecken per dokument och du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i meddelandetexten i begäran. I följande exempel är en illustration av innehåll som du kan skicka för viktiga frasen extrahering.

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```    
    
## <a name="step-1-structure-the-request"></a>Steg 1: Struktur begäran

Information om begäran definition finns i [hur du anropar API: et för Text Analytics](text-analytics-how-to-call-api.md). Följande punkter räknas av praktiska skäl:

+ Skapa en **POST** begäran. Granska API-dokumentationen för denna begäran: [nyckel fraser API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Ange HTTP-slutpunkten för viktiga frasen extrahering. Det måste innehålla den `/keyphrases` resursen: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ Ange ett huvud med snabbtangenten för Text Analytics-åtgärder. Mer information finns i [hur du hittar slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange samlingen JSON-dokument du förberedd för den här analysen i frågans brödtext

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna den **API-testet konsolen** i den [dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) struktur en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analys utförs vid mottagning av begäran. Tjänsten accepterar upp till 100 begäranden per minut. Varje begäran kan vara upp till 1 MB.

Kom ihåg att tjänsten är tillståndslösa. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.

## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Alla POST-förfrågningar returnera en JSON-formaterad svar med ID: N och egenskaper identifierades.

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör att du kan sortera, söka och manipulera data.

Ett exempel på utdata för viktiga frasen extrahering visas nästa:

```
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

Som anges i analyzer hittar Ignorerar ord som inte behövs och behåller enstaka ord eller fraser som verkar vara ämnet eller objekt i en mening. 

## <a name="summary"></a>Sammanfattning

I den här artikeln får du har lärt dig begrepp och arbetsflöde för viktiga frasen extrahering med hjälp av Text Analytics i kognitiva Services. Sammanfattningsvis:

+ [Nyckeln frasen extrahering API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) är tillgänglig för markerade språk.
+ JSON-dokument i begärandetexten innehåller ett id, text och språk-kod.
+ POST-begäran är att en `/keyphrases` slutpunkten, med hjälp av en anpassad [åtkomst till nyckeln och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för prenumerationen.
+ Svarsutdata som består av nyckel ord och fraser för varje dokument-ID, kan strömmas till någon app som accepterar JSON, inklusive Excel och Power BI att nämna några.

## <a name="see-also"></a>Se också 

 [Översikt över text Analytics](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics produktsidan](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
