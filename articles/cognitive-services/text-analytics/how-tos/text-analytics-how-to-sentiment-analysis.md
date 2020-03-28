---
title: Utföra sentimentanalys med REST API för textanalys
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du identifierar sentiment i text med AZURE Cognitive Services Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/09/2020
ms.author: aahi
ms.openlocfilehash: b3c112876bfd2578e6ebaa95c6902aa9b8f832d9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203465"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Så här identifierar du sentiment med hjälp av API:et för textanalys

Funktionen Sentimentanalys för Text Analytics utvärderar text och returnerar sentimentpoäng och etiketter för varje mening. Detta är användbart för att upptäcka positiva och negativa känslor i sociala medier, kundrecensioner, diskussionsforum och mycket mer. AI-modeller som används av API tillhandahålls av tjänsten, du behöver bara skicka innehåll för analys.

> [!TIP]
> Textanalys ger även en Linux-baserad Docker-containeravbildning för språkidentifiering, så att du kan [installera och köra Textanalys-containern](text-analytics-how-to-install-containers.md) nära dina data.

Sentimentanalys stöder ett brett spektrum av språk, med mer i förhandsgranskning. Mer information finns i [språk som stöds](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Begrepp

Api:et för textanalys använder en klassificeringsalgoritm för maskininlärning för att generera en sentimentpoäng mellan 0 och 1. Resultat nära 1 visar positiv attityd medan resultat nära 0 indikerar negativ attityd. Sentimentanalys utförs på hela dokumentet i stället för enskilda entiteter i texten. Det innebär att sentimentpoäng returneras på dokument- eller meningsnivå. 

Den modell som används är förutbildad med en omfattande korpus av text- och sentimentföreningar. Den använder en kombination av tekniker för analys, inklusive textbehandling, del-of-tal analys, ordplacering och ordassociationer. Läs mer om algoritmen [Introduktion till textanalys](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). För närvarande är det inte möjligt att tillhandahålla dina egna träningsdata. 

Det finns en tendens till att bedömningsprecisionen förbättras när dokument innehåller färre meningar i stället för ett stort textblock. Under en objektivitetsutvärderingsfasen avgör modellen om ett dokument som helhet är objektivt eller innehåller attityd. Ett dokument som oftast är objektivt går inte vidare till sentimentidentifieringsfasen, vilket resulterar i en 0,50-poäng, utan ytterligare bearbetning. För dokument som fortsätter på gång genererar nästa fas en poäng över eller under 0,50. Poängen beror på graden av sentiment som identifieras i dokumentet.

## <a name="sentiment-analysis-versions-and-features"></a>Sentimentanalysversioner och funktioner

Api:et för textanalys erbjuder två versioner av Sentiment Analysis - v2 och v3. Sentiment Analysis v3 (Offentlig förhandsversion) ger betydande förbättringar i noggrannheten och detaljriket i API:ets textkategorisering och bedömning.

> [!NOTE]
> * Sentimentanalys v3-begärandeformat och [datagränser](../overview.md#data-limits) är desamma som den tidigare versionen.
> * Sentimentanalys v3 är tillgängligt i `Australia East` `Central Canada`följande `Central US` `East Asia`regioner: `East US 2` `North Europe`, , `UK South` `West Europe`, `West US 2` `East US`, , , , `Southeast Asia` `South Central US`, , , , , och .

| Funktion                                   | Sentimentanalys v2 | SentimentAnalys v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Metoder för enstaka och batchbegäranden    | X                     | X                     |
| Sentimentpoäng för hela dokumentet  | X                     | X                     |
| Sentimentpoäng för enskilda meningar |                       | X                     |
| Sentimentetikett                        |                       | X                     |
| Versionshantering av modell                   |                       | X                     |

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

### <a name="sentiment-scoring"></a>Bedömning av sentiment

Sentimentanalys v3 klassificerar text med sentimentetiketter (beskrivs nedan). De returnerade poängen representerar modellens förtroende för att texten antingen är positiv, negativ eller neutral. Högre värden innebär högre förtroende. 

### <a name="sentiment-labeling"></a>Sentimentetikett

Sentimentanalys v3 kan returnera poäng och etiketter på en menings- och dokumentnivå. Poängen och etiketterna `negative`är `neutral` `positive`, och . På dokumentnivå kan `mixed` sentimentetiketten också returneras utan poäng. Känslan i dokumentet bestäms nedan:

| Meningssinne                                                                            | Returnerad dokumentetikett |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Minst en `positive` mening finns med i dokumentet. Resten av meningarna `neutral`är . | `positive`              |
| Minst en `negative` mening finns med i dokumentet. Resten av meningarna `neutral`är . | `negative`              |
| Minst en `negative` mening och `positive` minst en mening finns med i dokumentet.    | `mixed`                 |
| Alla meningar i `neutral`dokumentet är .                                                  | `neutral`               |

### <a name="model-versioning"></a>Versionshantering av modell

> [!NOTE]
> Modellversion för sentimentanalys är tillgänglig `v3.0-preview.1`från och med version .

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Exempel C#-kod

Du hittar ett exempel C# program som anropar den här versionen av Sentiment Analysis på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs).


#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

### <a name="sentiment-scoring"></a>Bedömning av sentiment

Sentimentanalysatorn klassificerar text som övervägande positiv eller negativ. Den tilldelar en poäng i intervallet 0 till 1. Värden nära 0,5 är neutrala eller obestämda. Ett resultat på 0,5 anger neutralitet. När en sträng inte kan analyseras för sentiment eller inte har någon känsla, är poängen alltid 0,5 exakt. Om du skickar in en spansk sträng i kod för engelska är poängen 0,5.

---

## <a name="sending-a-rest-api-request"></a>Skicka en REST API-begäran 

### <a name="preparation"></a>Förberedelse

Sentimentanalys ger ett resultat av högre kvalitet när du ger den mindre mängder text att arbeta med. Detta är motsatsen till nyckelfrasextrahering vilket fungerar på bättre på större textblock. För att få bästa resultat från båda åtgärder kan du överväga att omstrukturera indata därefter.

Du måste ha JSON-dokument i det här formatet: ID, text och språk.

Dokumentstorleken måste vara mindre än 5 120 tecken per dokument. Du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten.

## <a name="structure-the-request"></a>Strukturera begäran

Skicka en POST-begäran. Du kan [använda Postman](text-analytics-how-to-call-api.md) eller **API-testkonsolen** i följande referenslänkar för att snabbt strukturera och skicka en. 

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

[Sentimentanalys v3-referens](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[Sentimentanalys v2-referens](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Ange HTTPS-slutpunkten för sentimentanalys med hjälp av antingen en Text Analytics-resurs på Azure eller en instansierad [Text Analytics-behållare](text-analytics-how-to-install-containers.md). Du måste inkludera rätt URL för den version du vill använda. Ett exempel:

> [!NOTE]
> Du hittar din nyckel och slutpunkt för din Text Analytics-resurs på Azure-portalen. De kommer att finnas på resursens **snabbstartssida** under **resurshantering**. 

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Ange att en begäran ska innehålla API-nyckeln för textanalys. Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

### <a name="example-sentiment-analysis-request"></a>Exempel på begäran om sentimentanalys 

Följande är ett exempel på innehåll som du kan skicka in för attitydanalys. Formatet för begäran är detsamma för båda versionerna av API:et.
    
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

### <a name="post-the-request"></a>Publicera begäran

Analysen utförs när begäran har tagits emot. Information om storleken och antalet begäranden som du kan skicka per minut och sekund finns i avsnittet [datagränser](../overview.md#data-limits) i översikten.

API:et för textanalys är tillståndslöst. Inga data lagras i ditt konto och resultaten returneras omedelbart i svaret.


### <a name="view-the-results"></a>Visa resultatet

Sentimentanalysatorn klassificerar text som övervägande positiv eller negativ. Den tilldelar en poäng i intervallet 0 till 1. Värden nära 0,5 är neutrala eller obestämda. Ett resultat på 0,5 anger neutralitet. När en sträng inte kan analyseras för sentiment eller inte har någon känsla, är poängen alltid 0,5 exakt. Om du skickar in en spansk sträng i kod för engelska är poängen 0,5.

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil i det lokala systemet. Importera sedan utdata till ett program som du kan använda för att sortera, söka efter och ändra data. På grund av stöd för flerspråkiga och emoji kan svaret innehålla textförskjutningar. Se [hur du bearbetar förskjutningar](../concepts/text-offsets.md) för mer information.

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Sentiment Analysis v3 exempel svar

Svar från Sentiment Analysis v3 innehåller sentimentetiketter och poäng för varje analyserad mening och dokument. `documentScores`returneras inte om dokument sentimentetiketten är `mixed`.

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

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>SentimentAnalys v2 exempel svar

Svar från Sentiment Analysis v2 innehåller sentimentpoäng för varje skickat dokument.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöde för sentimentanalys med hjälp av API:et för textanalys. Sammanfattningsvis:

+ Sentimentanalys är tillgängligt för valda språk i två versioner.
+ JSON-dokument i begäran innehåller ett ID, text och språkkod.
+ POST-begäran är `/sentiment` till en slutpunkt med hjälp av en anpassad [åtkomstnyckel och en slutpunkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som gäller för din prenumeration.
+ Svarsutdata, som består av en sentimentpoäng för varje dokument-ID, kan strömmas till alla appar som accepterar JSON. Excel och Power BI.

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Använda klientbiblioteket Text Analytics](../quickstarts/text-analytics-sdk.md)
* [Nyheter](../whats-new.md)
