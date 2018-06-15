---
title: Sentiment kognitiva Sök kunskaper (Azure Search) | Microsoft Docs
description: Extrahera sentiment från texten i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1ddbba5b881cd05a997cd24a9396d5b722376e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791002"
---
#   <a name="sentiment-cognitive-skill"></a>Sentiment kognitiva kunskaper

Den **Sentiment** kunskaper utvärderar Ostrukturerade text längs en positiv negativ produktserier och för varje post, returneras ett numeriskt värde mellan 0 och 1. Poäng nära 1 anger positiv känsla och poäng nära 0 anger negativ känsla.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 5 000 tecken med `String.Length`. Om du vill dela upp dina data innan den skickas till sentiment analyzer, Använd den [Text dela kunskaper](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Kunskaper parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn |                      |
|----------------|----------------------|
| defaultLanguageCode | (valfritt) Språkkoden som ska användas för dokument som inte uttryckligen anger språk. <br/> Se [fullständig lista över språk som stöds](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Kunskaper indata 

| Indatanamnet | Beskrivning |
|--------------------|-------------|
| Text | Texten som ska analyseras.|
| languageCode  |  (Valfritt) En sträng som anger språk poster. Om den här parametern inte anges är standardvärdet ”SV”. <br/>Se [fullständig lista över språk som stöds](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Kunskaper utdata

| Namnet på målet | Beskrivning |
|--------------------|-------------|
| Poäng | Ett värde mellan 0 och 1 som representerar sentiment analyserade text. Värden nära 0 har negativ sentiment, nära 0,5 har neutral sentiment och värden nära 1 har positiva sentiment.|


##  <a name="sample-definition"></a>Exempel definition

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
Om den är tom returneras inte sentiment poängen för dessa poster.

## <a name="error-cases"></a>Fel
Om ett språk inte stöds, genereras ett fel och ingen sentiment resultat returneras.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)