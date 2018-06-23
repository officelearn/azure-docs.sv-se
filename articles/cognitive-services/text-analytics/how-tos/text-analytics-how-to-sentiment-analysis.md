---
title: Anvisningar sentiment analys i Text Analytics REST-API (Microsoft kognitiva tjänster i Azure) | Microsoft Docs
description: Hur man upptäcker sentiment med Text Analytics REST API i kognitiva Microsoft-tjänster i Azure i den här genomgången självstudiekursen.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 12/11/2017
ms.author: heidist
ms.openlocfilehash: 7ffd8bbe47409b459fdd308cd8d670d32f56649b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352236"
---
# <a name="how-to-detect-sentiment-in-text-analytics"></a>Hur du identifierar sentiment i textanalys

Den [Sentiment analys API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) utvärderar textinmatning och returnerar en sentiment poäng för varje dokument, mellan 0 (negativa) och 1 (positiv).

Den här funktionen är användbart för att upptäcka positiva och negativa sentiment i sociala medier, Kundrecensioner och diskussionsforum. Innehållet kommer från. modeller och träningsdata tillhandahålls av tjänsten.

Sentiment analys stöder för närvarande, engelska, tyska, spanska och franska. Andra språk finns i förhandsgranskningen. Mer information finns i [språk som stöds](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Begrepp

Textanalys använder en klassificering i maskininlärningsalgoritmen för att generera en sentiment poäng på mellan 0 och 1. Poäng närmare 1 anger positivt sentiment medan poäng närmare 0 indikerar negativa sentiment. Modellen pretrained med en omfattande brödtext med sentiment kopplingarna. Det är för närvarande inte möjligt att tillhandahålla utbildningsdata. Modellen använder en kombination av metoder vid analys av text, inklusive text bearbetning, en del av tal analys, word placering och word kopplingarna. Läs mer om algoritmen [introduktion till textanalys](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Sentiment analys utförs på hela dokumentet, till skillnad från extraherar sentiment för en viss enhet i texten. I praktiken innebär finns det en tendens för resultatfunktioner noggrannhet att förbättra när dokument innehåller en eller två meningar i stället för ett stort textblock. Under en objektivitet kompatibilitetsutvärderingsfasen avgör modellen om ett dokument som helhet mål eller innehåller sentiment. Ett dokument som är mest mål har inte pågående sentiment identifiering fras, vilket resulterar i en.50 poäng, med ingen ytterligare bearbetning. Nästa fas genererar en poäng ovanför eller nedanför.50, beroende på vilken grad av sentiment upptäcktes i dokumentet om fortsätter i pipeline-dokument.

## <a name="preparation"></a>Förberedelse

Sentiment analys ger högre kvalitetsresultatet när du ger mindre segment för att arbeta med. Detta är motsatt från viktiga frasen extrahering, vilken som presterar bäst på större textblock. Överväg att omstrukturera indata i enlighet med detta för att få bästa möjliga resultat från båda åtgärder.

Du måste ha JSON-dokument i det här formatet: id, text, språk

Storlek måste vara under 5 000 tecken per dokument och du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i meddelandetexten i begäran. Följande är ett exempel på innehåll som du kan skicka för sentiment analys.

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

+ Skapa en **POST** begäran. Granska API-dokumentationen för denna begäran: [Sentiment analys API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ Ange HTTP-slutpunkten för viktiga frasen extrahering. Det måste innehålla den `/sentiment` resursen: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`

+ Ange ett huvud med snabbtangenten för Text Analytics-åtgärder. Mer information finns i [hur du hittar slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange samlingen JSON-dokument du förberedd för den här analysen i frågans brödtext.

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna den **API-testet konsolen** i den [dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) struktur begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analys utförs vid mottagning av begäran. Tjänsten accepterar upp till 100 begäranden per minut. Varje begäran kan vara upp till 1 MB.

Kom ihåg att tjänsten är tillståndslösa. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.


## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Sentiment analyzer klassificerar text som främst positivt eller negativt, tilldela ett värde i intervallet 0 till 1. Nära 0,5 värdena neutral eller obestämt. Ett resultat på 0,5 anger neutralitet. När en sträng inte analyseras för sentiment eller har ingen sentiment, poängsättningen är alltid 0,5 exakt. Om du skickar i en spanska sträng engelska kod är poängsättningen 0,5.

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör att du kan sortera, söka och manipulera data.

I följande exempel visas svaret för dokumentsamlingen i den här artikeln.

```
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="summary"></a>Sammanfattning

I den här artikeln får du har lärt dig begrepp och arbetsflöde för sentiment analys med hjälp av Text Analytics i kognitiva Services. Sammanfattningsvis:

+ [Sentiment analys API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) är tillgänglig för markerade språk.
+ JSON-dokument i begärandetexten innehåller ett id, text och språk-kod.
+ POST-begäran är att en `/sentiment` slutpunkten, med hjälp av en anpassad [åtkomst till nyckeln och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för prenumerationen.
+ Svarsutdata som består av en sentiment poäng för varje dokument-ID, kan strömmas till någon app som accepterar JSON, inklusive Excel och Power BI att nämna några.

## <a name="see-also"></a>Se också 

 [Översikt över text Analytics](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics produktsidan](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera nyckeln fraser](text-analytics-how-to-keyword-extraction.md)
