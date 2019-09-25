---
title: Sentiment-analys med hjälp av Textanalys REST API från Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Lär dig att identifiera sentiment med hjälp av Textanalys REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: ea145239d38a4030423a4517fe02c62b8eefa08a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211769"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Exempel: Identifiera sentiment med Textanalys

[Azure ATTITYDANALYS API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) utvärderar text ingångar och returnerar en sentiment Poäng för varje dokument. Resultat intervallet är 0 (negativt) till 1 (positivt).

Den här funktionen är användbar för att upptäcka positiva och negativa åsikter i sociala medier, kundrecensioner och diskussionsforum. Innehåll tillhandahålls av dig. Modeller och tränings data tillhandahålls av tjänsten.

För närvarande stöder Attitydanalys API: t engelska, tyska, spanska och franska. Övriga språk är i förhandsversion. Mer information finns i [språk som stöds](../text-analytics-supported-languages.md).

> [!TIP]
> Azure API för textanalys tillhandahåller också en Linux-baserad Docker-behållar avbildning för sentiment-analys, så att du kan [Installera och köra textanalys-behållaren](text-analytics-how-to-install-containers.md) nära dina data.

## <a name="concepts"></a>Begrepp

Textanalys använder en klassificering i maskininlärningsalgoritmen för att generera ett attitydpoäng mellan 0 och 1. Resultat nära 1 visar positiv attityd medan resultat nära 0 indikerar negativ attityd. Modellen har förtränats med en omfattande textmassa med attitydassociationer. För närvarande går det inte att tillhandahålla dina egna tränings data. Modellen använder en kombination av metoder under text analys. Tekniker omfattar text bearbetning, del av tal analys, ord placering och Word-associationer. Läs mer om algoritmen [Introduktion till textanalys](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Attitydanalys utförs i hela dokumentet, till skillnad från åsiktsextrahering för en viss affärsenhet i texten. I praktiken finns det en tendens för att förbättra precisionen när dokumenten innehåller en eller två meningar i stället för ett stort block med text. Under en objektivitetsutvärderingsfasen avgör modellen om ett dokument som helhet är objektivt eller innehåller attityd. Ett dokument som främst är målet går inte vidare till identifierings fasen sentiment, vilket resulterar i en 0,50 poäng, utan ytterligare bearbetning. För dokument som fortsätter i pipelinen genererar nästa fas en poäng över eller under 0,50. Poängen beror på vilken grad av sentiment som har identifierats i dokumentet.

## <a name="preparation"></a>Förberedelse

Sentiment-analys ger bättre resultat när du ger den mindre text segment att arbeta med. Detta är motsatsen till nyckelfrasextrahering vilket fungerar på bättre på större textblock. För att få bästa resultat från båda åtgärder kan du överväga att omstrukturera indata därefter.

Du måste ha JSON-dokument i det här formatet: ID, text och språk.

Dokument storleken måste vara under 5 120 tecken per dokument. Du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten. Följande exempel är ett exempel på innehåll som du kan skicka för sentiment-analys:

```json
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

Mer information om definition av begäran finns i [anropa API för textanalys](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skapa en POST-begäran. Information om hur du granskar API-dokumentationen för den här begäran finns i [ATTITYDANALYS API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Ange HTTP-slutpunkten för sentiment-analys genom att antingen använda en Textanalys-resurs på Azure eller en instansierad [textanalys-behållare](text-analytics-how-to-install-containers.md). Du måste ta `/text/analytics/v2.1/sentiment` med i URL: en. Till exempel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`.

+ Ange ett rubrik för begäran för att inkludera [åtkomst nyckeln](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) för textanalys åtgärder.

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API test-konsolen** i [dokumentationen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) för att strukturera begäran och publicera den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analysen utförs när begäran har tagits emot. Information om storlek och antal begär Anden som du kan skicka per minut och sekund finns i avsnittet [data begränsningar](../overview.md#data-limits) i översikten.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.


## <a name="step-3-view-the-results"></a>Steg 3: Visa resultatet

Sentiment Analyzer klassificerar texten som huvudsakligen positiv eller negativ. Det tilldelar en poäng mellan 0 och 1. Värden nära 0,5 är neutrala eller obestämda. Ett resultat på 0,5 anger neutralitet. När en sträng inte kan analyseras för sentiment eller saknar sentiment är poängen alltid 0,5 exakt. Om du skickar in en spansk sträng i kod för engelska är poängen 0,5.

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil på det lokala systemet. Importera sedan utdata till ett program som du kan använda för att sortera, söka och ändra data.

I följande exempel visas svaret på dokument samlingen i den här artikeln:

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

## <a name="sentiment-analysis-v3-public-preview"></a>Attitydanalys v3 offentlig för hands version

[Nästa version av Attitydanalys](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) är nu tillgänglig för offentlig för hands version. Den ger betydande förbättringar av precisionen och detaljerna i API: ns text kategorisering och poängsättning.

> [!NOTE]
> * Formatet för förfrågnings formatet för Attitydanalys v3 och [data begränsningarna](../overview.md#data-limits) är desamma som i den tidigare versionen.
> * Attitydanalys v3 vid detta tillfälle:
>    * Stöder för närvarande engelska, franska, italienska, japanska, förenklade och traditionella kinesiska språk.
>    * Är tillgängligt i `Australia East`följande regioner:, `East US 2` ,`West US 2` ,, `East Asia`,,,,,, och `Central US` `Central Canada` `East US` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` .

|Funktion |Beskrivning  |
|---------|---------|
|Förbättrad precision     | Funktionen är mycket bättre på att identifiera positiva, neutrala, negativa och blandade sentiment i textdokument jämfört med tidigare versioner.           |
|Sentiment Poäng för dokument och menings nivåer     | Identifiera sentiment i dokumentet och i de enskilda meningar. Om dokumentet innehåller flera meningar tilldelas även de enskilda meningarna sentimentpoäng.         |
|Sentiment-kategori och Poäng     | API: et returnerar nu sentiment-kategorier för text, förutom en sentiment poäng. `positive`Kategorierna är `negative` ,,och`mixed`. `neutral`       |
| Förbättrade utdata | Sentiment analys returnerar nu information för både ett helt text dokument och dess enskilda meningar. |

### <a name="sentiment-labeling"></a>Sentiment-etikettering

Attitydanalys v3 kan returnera Poäng och etiketter på en menings-och dokument nivå. Poängen och etiketterna är `positive`, `negative`och `neutral`. På dokument nivå kan du `mixed` även returnera sentiment-etiketten (inte poängen). Sentiment av dokumentet bestäms genom att aggregera poängen i meningarna.

| Mening sentiment                                                        | Etikett för returnerat dokument |
|---------------------------------------------------------------------------|----------------|
| Minst en positiv mening och resten av meningarna är neutrala. | `positive`     |
| Minst en negativ mening och resten av meningarna är neutrala.  | `negative`     |
| Minst en negativ mening och minst en positiv mening.         | `mixed`        |
| Alla meningar är neutrala.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Exempel förfrågan om Attitydanalys v3

Följande JSON är ett exempel på en begäran som gjorts till den nya versionen av Attitydanalys. Den begärda formateringen är samma som den tidigare versionen:

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
        ],
    }
```

### <a name="sentiment-analysis-v3-example-response"></a>Exempel svar för Attitydanalys v3

Formatet för begäran är detsamma som i föregående version, men svars formatet har ändrats. Följande JSON är ett exempel på ett svar från den nya versionen av API: et:

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

Du kan hitta ett exempel C# program som anropar den här versionen av Attitydanalys på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för sentiment-analys genom att använda Textanalys i Azure Cognitive Services. Sammanfattningsvis:

+ [Attitydanalys-API: et](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) är tillgängligt för valda språk.
+ JSON-dokument i begär ande texten innehåller ID, text och språkkod.
+ POST-begäran är till en `/sentiment` slut punkt genom att använda en anpassad [åtkomst nyckel och en slut punkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som är giltig för din prenumeration.
+ Svars utdata som består av en sentiment Poäng för varje dokument-ID kan strömmas till alla appar som accepterar JSON. Exempel på appar är Excel och Power BI, så att du kan namnge några.

## <a name="see-also"></a>Se också

 [Översikt över Textanalys](../overview.md) [Vanliga frågor och svar](../text-analytics-resource-faq.md)</br>
 [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera nyckelfraser](text-analytics-how-to-keyword-extraction.md)
