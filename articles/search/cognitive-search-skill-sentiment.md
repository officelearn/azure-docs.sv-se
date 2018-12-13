---
title: Sentiment kognitiv sökning färdigheter – Azure Search
description: Extrahera en positiv negativ sentimentresultatet från texten i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 4b8913d64a3df8799ba1d73972121ef331aaac81
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314082"
---
#   <a name="sentiment-cognitive-skill"></a>Sentiment kognitiva kunskaper

Den **Sentiment** färdighet utvärderar ostrukturerad text längs en positiv negativ omfattningen och för varje post, returnerar ett numeriskt värde mellan 0 och 1. Poäng nära 1 anger positiv känsla och poäng nära 0 anger negativ känsla.

> [!NOTE]
> Från och med den 21 December 2018 kan du associera en resurs för Cognitive Services med ett Azure Search-kompetens. Detta gör att vi börjar debitera för körning av kompetens. På det här datumet måste också börjar vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.
>
> Körningen av inbyggda kunskaper debiteras med den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/) . Bild extrahering priser kommer att debiteras enligt priserna för förhandsversionen och beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400). Lär dig [mer](cognitive-search-attach-cognitive-services.md).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 5 000 tecken enligt `String.Length`. Om du vill dela upp dina data innan de skickas till sentiment analyzer, Använd den [Text dela färdighet](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Färdighet parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn |                      |
|----------------|----------------------|
| defaultLanguageCode | (valfritt) Språkkoden som ska användas för dokument som inte uttryckligen anger språk. <br/> Se [fullständig lista över språk som stöds](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Färdighet indata 

| Indatanamnet | Beskrivning |
|--------------------|-------------|
| text | Texten som ska analyseras.|
| languageCode  |  (Valfritt) En sträng som anger språket poster. Om den här parametern inte anges är standardvärdet ”SV”. <br/>Se [fullständig lista över språk som stöds](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Färdighet utdata

| Namn på utdata | Beskrivning |
|--------------------|-------------|
| poäng | Ett värde mellan 0 och 1 som representerar emotionella Analyserad text. Värden nära 0 har negativ känsla, nära 0,5 har neutral känsla och värden nära 1 har positiv attityd.|


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

##  <a name="sample-input"></a>Exempelindata

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
Om den är tom returneras inte ett sentimentpoäng för dessa poster.

## <a name="error-cases"></a>Felhändelser
Om ett språk inte stöds, genereras ett fel och ingen sentimentresultatet returneras.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)