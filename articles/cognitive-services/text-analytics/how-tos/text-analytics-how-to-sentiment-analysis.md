---
title: Attitydanalys med hjälp av REST API för textanalys från Azure Cognitive Services | Microsoft Docs
description: Lär dig att identifiera sentiment med hjälp av den REST API för textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: c3004dd3910dd5fdafc933efa213c9f097310e87
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001717"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Exempel: Identifiera sentiment med textanalys

Den [API: T för analys av Azure Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) utvärderar textinmatning och returnerar ett sentimentpoäng för varje dokument. Poäng mellan 0 (negativ) och 1 (positiv).

Den här funktionen är användbar för att upptäcka positiva och negativa åsikter i sociala medier, kundrecensioner och diskussionsforum. Innehåll tillhandahålls av dig. Modeller och träningsdata tillhandahålls av tjänsten.

API för Attitydstextanalys Analysis stöder för närvarande, engelska, tyska, spanska och franska. Övriga språk är i förhandsversion. Mer information finns i [språk som stöds](../text-analytics-supported-languages.md).

> [!TIP]
> Azure API för textanalys ger också en Linux-baserade Docker-behållaravbildning för attitydanalys, så att du kan [installera och köra behållaren textanalys](text-analytics-how-to-install-containers.md) nära dina data.

## <a name="concepts"></a>Begrepp

Textanalys använder en klassificering i maskininlärningsalgoritmen för att generera ett attitydpoäng mellan 0 och 1. Resultat nära 1 visar positiv attityd medan resultat nära 0 indikerar negativ attityd. Modellen har förtränats med en omfattande textmassa med attitydassociationer. För närvarande kan den inte är möjligt att ange dina egna träningsdata. Modellen använder en kombination av metoder under textanalys. Tekniken omfattar bearbetning av text, av ordklasser analys, word placering och word associationer. Läs mer om algoritmen [Introduktion till textanalys](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Attitydanalys utförs i hela dokumentet, till skillnad från åsiktsextrahering för en viss affärsenhet i texten. I praktiken är det en tendens för bedömning Precision för att förbättra när dokument innehåller en eller två meningar i stället för ett stort textblock. Under en objektivitetsutvärderingsfasen avgör modellen om ett dokument som helhet är objektivt eller innehåller attityd. Ett dokument som är mest mål inte vidare till fasen sentiment identifiering, vilket resulterar i en 0,50 poäng, med ingen vidare bearbetning. Nästa fas genererar en poäng över eller under 0,50 dokument som fortsätta i pipelinen. Poängen beror på graden av attityd som identifierats i dokumentet.

## <a name="preparation"></a>Förberedelse

Attitydanalys ger högre kvalitet resultatet när du ger det mindre segment för att arbeta med. Detta är motsatsen till nyckelfrasextrahering vilket fungerar på bättre på större textblock. För att få bästa resultat från båda åtgärder kan du överväga att omstrukturera indata därefter.

Du måste ha JSON-dokument i det här formatet: ID, text och språk.

Dokumentstorlek måste vara under 5,120 tecken per dokument. Du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten. I följande exempel är ett exempel på innehåll som du kan skicka in för attitydanalys:

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

## <a name="step-1-structure-the-request"></a>Steg 1: Strukturera begäran

Läs mer på begäran definition [anropa API för textanalys](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skapa en POST-begäran. API-dokumentationen för den här begäran finns i den [API för Attitydstextanalys Analysis](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Ange HTTP-slutpunkt för attitydanalys med hjälp av antingen en Text Analytics-resurs på Azure eller en initierad [textanalys behållare](text-analytics-how-to-install-containers.md). Det måste innehålla den `/sentiment` resurs: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`.

+ Ange en begäransrubrik som inkluderar åtkomstnyckeln för textanalysåtgärder. Mer information finns i [hitta slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna den **API testkonsolen** i den [dokumentation](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) att strukturera begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analysen utförs när begäran har tagits emot. Information om storlek och antal begäranden kan du skicka per minut och sekund, visa den [databegränsningar](../overview.md#data-limits) avsnittet i översikten.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.


## <a name="step-3-view-the-results"></a>Steg 3: Visa resultatet

Sentiment analysatorn klassificerar text som främst positivt eller negativt. Det tilldelar en poäng i intervallet 0 till 1. Värden nära 0,5 är neutrala eller obestämda. Ett resultat på 0,5 anger neutralitet. När en sträng inte analyseras efter attityd eller har ingen sentiment, poängen är alltid 0,5 exakt. Om du skickar in en spansk sträng i kod för engelska är poängen 0,5.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på det lokala systemet. Importera sedan utdata till ett program som du kan använda för att sortera, söka och manipulera data.

I följande exempel visas svaret för dokumentsamlingen i den här artikeln:

```json
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

## <a name="sentiment-analysis-v3-public-preview"></a>Sentiment Analysis v3 offentlig förhandsversion

Den [nästa version av Attitydanalys](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) är nu tillgänglig i offentlig förhandsversion. Det ger betydande förbättringar i Precision och information om API: er text kategorisering och bedömning. 

> [!NOTE]
> * Format för förfrågan Attitydanalys v3 och [databegränsningar](../overview.md#data-limits) är samma som den tidigare versionen.
> * Just nu, Attitydanalys v3: 
>    * Stöder för närvarande endast på engelska.  
>    * Är tillgängligt i följande regioner: `Central US`, `Central Canada`, och `East Asia`.

|Funktion |Beskrivning  |
|---------|---------|
|Förbättrar precisionen     | Funktionen är mycket bättre på att identifiera positiva, neutrala, negativa och blandade sentiment i textdokument jämfört med tidigare versioner.           |
|Dokumentera och meningsnivå sentimentresultatet     | Identifiera sentiment i dokumentet och i de enskilda meningar. Om dokumentet innehåller flera meningar tilldelas även de enskilda meningarna sentimentpoäng.         |
|Kategori för känsla och poäng     | API: et returnerar nu sentiment kategorier för text, förutom ett sentimentpoäng. Kategorierna är `positive`, `negative`, `neutral`, och `mixed`.       |
| Förbättrad utdata | Attitydanalys returnerar nu information för både en hel textdokument och dess meningar. |

### <a name="sentiment-labeling"></a>Sentiment märkning

Sentiment Analysis v3 kan returnera resultat och etiketter på en mening och dokument. Poängen och etiketter är `positive`, `negative`, och `neutral`. På dokumentnivå, den `mixed` etikettens (inte poäng) kan också kan returneras. Sentimentvärdet för dokumentet bestäms av aggregering av meningarna.

| Mening sentiment                                                        | Returnerade dokumentetikett |
|---------------------------------------------------------------------------|----------------|
| Minst en positiv mening och resten av meningarna är neutral. | `positive`     |
| Minst en negativ mening och resten av meningarna är neutral.  | `negative`     |
| Minst en negativ mening och minst en positiv mening.         | `mixed`        |
| Alla meningar är neutrala.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Sentiment Analysis v3 exempelbegäran

Följande JSON är ett exempel på en begäran till den nya versionen av Attitydanalys. Begäran formateringen är samma som den tidigare versionen:

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Hello world. This is some input text that I love."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    }
  ]
}
```

### <a name="sentiment-analysis-v3-example-response"></a>Sentiment Analysis v3 exempelsvar

Format för förfrågan är samma som den tidigare versionen, har svarsformat ändrats. Följande JSON är ett exempel på ett svar från den nya versionen av API: et:

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

### <a name="example-c-code"></a>Exempel C# kod

Du kan se ett exempel C# program som anropar den här versionen av Attitydanalys på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöde för attitydanalys med textanalys i Azure Cognitive Services. Sammanfattningsvis:

+ Den [API för Attitydstextanalys Analysis](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) är tillgänglig för valda språken.
+ JSON-dokument i begärandetexten innehålla ett ID, text och språk-kod.
+ POST-begäran är att en `/sentiment` slutpunkt med hjälp av en personligt anpassad [åtkomst till nyckeln och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för prenumerationen.
+ Svarsutdata som består av ett sentimentpoäng för varje dokument-ID, kan strömmas till någon app som tar emot JSON. Exempelappar innehåller Excel och Power BI för att nämna några.

## <a name="see-also"></a>Se också 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera nyckelfraser](text-analytics-how-to-keyword-extraction.md)
