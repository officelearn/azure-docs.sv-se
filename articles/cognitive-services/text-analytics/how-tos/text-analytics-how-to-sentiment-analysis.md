---
title: Utför sentiment-analys med Textanalys REST API
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du kan identifiera sentiment i text med Azure Cognitive Services Textanalys REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/27/2020
ms.author: aahi
ms.openlocfilehash: 99a62daf6dced88efd9bda591a0ca44a8b259a75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195646"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Så här: identifiera sentiment med hjälp av API för textanalys

API för textanalysens Attitydanalys-funktion utvärderar text och returnerar sentiment Poäng och etiketter för varje mening. Detta är användbart för att identifiera positiva och negativa sentiment i sociala medier, kund granskningar, diskussions forum med mera. AI-modellerna som används av API: et tillhandahålls av tjänsten. du behöver bara skicka innehåll för analys.

> [!TIP]
> Textanalys ger även en Linux-baserad Docker-containeravbildning för språkidentifiering, så att du kan [installera och köra Textanalys-containern](text-analytics-how-to-install-containers.md) nära dina data.

Attitydanalys stöder en mängd olika språk, med mer i för hands version. Mer information finns i [språk som stöds](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Begrepp

API för textanalys använder en algoritm för Machine Learning-klassificering för att generera en sentiment Poäng mellan 0 och 1. Resultat nära 1 visar positiv attityd medan resultat nära 0 indikerar negativ attityd. Sentiment-analys utförs i hela dokumentet, i stället för enskilda entiteter i texten. Det innebär att sentiment resultat returneras på dokument-eller menings nivå. 

Den modell som används är förtränad med en omfattande sökkorpus av text-och sentiment-associationer. Den använder en kombination av tekniker för analys, inklusive text bearbetning, del av tal analys, ord placering och Word-kopplingar. Läs mer om algoritmen [Introduktion till textanalys](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). För närvarande går det inte att tillhandahålla dina egna tränings data. 

Det finns en tendens för bedömnings precision att förbättra när dokument innehåller färre meningar i stället för ett stort block med text. Under en objektivitetsutvärderingsfasen avgör modellen om ett dokument som helhet är objektivt eller innehåller attityd. Ett dokument som främst är målet går inte vidare till identifierings fasen sentiment, vilket resulterar i en 0,50 poäng, utan ytterligare bearbetning. För dokument som fortsätter i pipelinen genererar nästa fas en poäng över eller under 0,50. Poängen beror på vilken grad av sentiment som har identifierats i dokumentet.

## <a name="sentiment-analysis-versions-and-features"></a>Attitydanalys versioner och funktioner

API för textanalys erbjuder två versioner av Attitydanalys-v2 och v3. Attitydanalys v3 (offentlig för hands version) ger betydande förbättringar av precisionen och detaljerna i API: ns text kategorisering och poängsättning.

> [!NOTE]
> * Formatet för förfrågnings formatet för Attitydanalys v3 och [data begränsningarna](../overview.md#data-limits) är desamma som i den tidigare versionen.
> * Attitydanalys v3 är `Australia East`tillgängligt i följande regioner:, `Central Canada`, `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe`,,,,,,,, och. `West US 2`

| Funktion                                   | Attitydanalys v2 | Attitydanalys v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Metoder för enkel-och batch-begäranden    | X                     | X                     |
| Sentiment resultat för hela dokumentet  | X                     | X                     |
| Sentiment resultat för enskilda meningar |                       | X                     |
| Sentiment-etikettering                        |                       | X                     |
| Modell version                   |                       | X                     |

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

### <a name="sentiment-scoring"></a>Sentiment-Poäng

Attitydanalys v3 klassificerar text med sentiment-etiketter (beskrivs nedan). De returnerade poängen representerar modellens förtroende för att texten är antingen positiv, negativ eller neutral. Högre värden indikerar högre tillförlitlighet. 

### <a name="sentiment-labeling"></a>Sentiment-etikettering

Attitydanalys v3 returnerar sentiment-etiketter på en mening och dokument nivå`positive`( `negative`, och `neutral`) tillsammans med förtroende poäng. `mixed` Sentiment-etiketten kan också returneras på dokument nivå. 

Sentiment för dokumentet fastställs nedan:

| Mening sentiment                                                                            | Etikett för returnerat dokument |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Minst en `positive` mening finns i dokumentet. Resten av meningarna är `neutral`. | `positive`              |
| Minst en `negative` mening finns i dokumentet. Resten av meningarna är `neutral`. | `negative`              |
| Minst en `negative` mening och minst en `positive` mening finns i dokumentet.    | `mixed`                 |
| Alla meningar i dokumentet är `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Modell version

> [!NOTE]
> Modell versions hantering för sentiment-analys är tillgänglig från `v3.0-preview.1`och med version.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Exempel C#-kod

Du kan hitta ett exempel på ett C#-program som anropar den här versionen av Attitydanalys på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs).


#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

### <a name="sentiment-scoring"></a>Sentiment-Poäng

Sentiment Analyzer klassificerar texten som huvudsakligen positiv eller negativ. Det tilldelar en poäng mellan 0 och 1. Värden nära 0,5 är neutrala eller obestämda. Ett resultat på 0,5 anger neutralitet. När en sträng inte kan analyseras för sentiment eller saknar sentiment är poängen alltid 0,5 exakt. Om du skickar in en spansk sträng i kod för engelska är poängen 0,5.

---

## <a name="sending-a-rest-api-request"></a>Skicka en REST API-begäran 

### <a name="preparation"></a>Förberedelse

Sentiment-analys ger bättre resultat när du ger det mindre mängd text att arbeta med. Detta är motsatsen till nyckelfrasextrahering vilket fungerar på bättre på större textblock. För att få bästa resultat från båda åtgärder kan du överväga att omstrukturera indata därefter.

Du måste ha JSON-dokument i det här formatet: ID, text och språk.

Dokument storleken måste vara under 5 120 tecken per dokument. Du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten.

## <a name="structure-the-request"></a>Strukturera begäran

Skicka en POST-begäran. Du kan [använda Postman](text-analytics-how-to-call-api.md) eller **konsolen för API-testning** i följande referens länkar för att snabbt strukturera och skicka en. 

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

[Attitydanalys v3-referens](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

[Referens för Attitydanalys v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Ange HTTPS-slutpunkten för sentiment-analys genom att antingen använda en Textanalys-resurs på Azure eller en instansierad [textanalys-behållare](text-analytics-how-to-install-containers.md). Du måste inkludera rätt URL för den version som du vill använda. Ett exempel:

> [!NOTE]
> Du kan hitta din nyckel och slut punkt för din Textanalys-resurs på Azure-portalen. De kommer att finnas på resursens **snabb start** sida under **resurs hantering**. 

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Ange ett rubrik för begäran som ska innehålla din API för textanalys-nyckel. Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

### <a name="example-sentiment-analysis-request"></a>Exempel Attitydanalys förfrågan 

Följande är ett exempel på innehåll som du kan skicka in för attitydanalys. Formatet för begäran är detsamma för båda versionerna av API: et.
    
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

Analysen utförs när begäran har tagits emot. Information om storlek och antal begär Anden som du kan skicka per minut och sekund finns i avsnittet [data begränsningar](../overview.md#data-limits) i översikten.

API för textanalys är tillstånds lös. Inga data lagras i ditt konto och resultaten returneras omedelbart i svaret.


### <a name="view-the-results"></a>Visa resultatet

Sentiment Analyzer klassificerar texten som huvudsakligen positiv eller negativ. Det tilldelar en poäng mellan 0 och 1. Värden nära 0,5 är neutrala eller obestämda. Ett resultat på 0,5 anger neutralitet. När en sträng inte kan analyseras för sentiment eller saknar sentiment är poängen alltid 0,5 exakt. Om du skickar in en spansk sträng i kod för engelska är poängen 0,5.

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil på det lokala systemet. Importera sedan utdata till ett program som du kan använda för att sortera, söka och ändra data. På grund av stöd för flerspråkig och emoji kan svaret innehålla text förskjutningar. Mer information finns i [så här bearbetar du förskjutningar](../concepts/text-offsets.md) .

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Exempel svar för Attitydanalys v3

Svar från Attitydanalys v3 innehåller sentiment etiketter och Poäng för varje analyserad mening och dokument. `documentScores`returneras inte om dokumentets sentiment-etikett är `mixed`.

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

#### <a name="version-21"></a>[Version 2,1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Exempel svar för Attitydanalys v2

Svar från Attitydanalys v2 innehåller sentiment resultat för varje skickat dokument.

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

I den här artikeln har du lärt dig begrepp och arbets flöde för sentiment-analys med hjälp av API för textanalys. Sammanfattningsvis:

+ Attitydanalys är tillgängligt för valda språk i två versioner.
+ JSON-dokument i begär ande texten innehåller ID, text och språkkod.
+ POST-begäran är till en `/sentiment` slut punkt genom att använda en anpassad [åtkomst nyckel och en slut punkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som är giltig för din prenumeration.
+ Svars utdata som består av en sentiment Poäng för varje dokument-ID kan strömmas till alla appar som accepterar JSON. Till exempel Excel och Power BI.

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Använda klient biblioteket för Textanalys](../quickstarts/text-analytics-sdk.md)
* [Nyheter](../whats-new.md)
