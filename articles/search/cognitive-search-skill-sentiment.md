---
title: Kognitiv skicklighet för känslor
titleSuffix: Azure Cognitive Search
description: Extrahera en positiv negativ sentimentpoäng från text i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791903"
---
# <a name="sentiment-cognitive-skill"></a>Kognitiv skicklighet för känslor

**Sentiment-färdigheten** utvärderar ostrukturerad text längs ett positivt negativt kontinuum och returnerar för varje post en numerisk poäng mellan 0 och 1. Poäng nära 1 indikerar positivt sentiment och poäng nära 0 indikerar negativt sentiment. Den här färdigheten använder de maskininlärningsmodeller som tillhandahålls av [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.sentimentskill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken på en post bör vara 5000 tecken mätt med [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Om du behöver dela upp dina data innan du skickar dem till sentimentanalysatorn använder du [textdelningsfärdigheten](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn |                      |
|----------------|----------------------|
| standardSpråkkod | (valfritt) Språkkoden som ska tillämpas på dokument som inte uttryckligen anger språk. <br/> Se [Fullständig lista över språk som stöds](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Indata för färdighet 

| Indatanamn | Beskrivning |
|--------------------|-------------|
| text | Texten som ska analyseras.|
| languageCode  |  (Valfritt) En sträng som anger posternas språk. Om den här parametern inte anges är standardvärdet "en". <br/>Se [Fullständig lista över språk som stöds](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Utdata för färdighet

| Utdatanamn | Beskrivning |
|--------------------|-------------|
| poäng | Ett värde mellan 0 och 1 som representerar sentimentet i den analyserade texten. Värden nära 0 har negativa sentiment, nära 0,5 har neutrala sentiment och värden nära 1 har positiva sentiment.|


##  <a name="sample-definition"></a>Exempeldefinition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
        }
    ]
}
```

##  <a name="sample-input"></a>Exempelinmatning

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Exempel på utdata

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Anteckningar
Om den är tom returneras inte en sentimentpoäng för dessa poster.

## <a name="error-cases"></a>Felfall
Om ett språk inte stöds genereras ett fel och ingen sentimentpoäng returneras.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
