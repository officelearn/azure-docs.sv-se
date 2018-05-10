---
title: Språk identifiering kognitiva Sök kunskaper (Azure Search) | Microsoft Docs
description: Utvärderar Ostrukturerade text och returnerar ett språk-ID för varje post med ett värde som anger styrkan hos analys i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 338d89b47ea451efcf8300d4ac016a6946a95259
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
#   <a name="language-detection-cognitive-skill"></a>Språk identifiering kognitiva kunskaper

För upp till 120 språk i **identifiera språk** kunskaper identifierar språket för indata och rapporterar en enda språkkod för varje dokument som skickades i begäran. Språkkoden paras ihop med en poäng som anger styrkan hos analys.

Den här funktionen är särskilt användbar när du behöver ange språket i texten som indata för andra kunskaper (till exempel den [Sentiment Snalysis kunskaper](cognitive-search-skill-sentiment.md) eller [Text dela kunskaper](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken med `String.Length`. Om du vill dela upp dina data innan den skickas till sentiment analyzer kan du använda den [Text dela kunskaper](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Kunskaper indata

Parametrar är skiftlägeskänsliga.

| Indata     | Beskrivning |
|--------------------|-------------|
| Text | Texten som ska analyseras.|

## <a name="skill-outputs"></a>Kunskaper utdata

| Namnet på målet    | Beskrivning |
|--------------------|-------------|
| languageCode | ISO 6391 språkkoden för det språk som identifierats. Till exempel ”SV”. |
| LanguageName | Namnet på språket. Till exempel ”English”. |
| Poäng | Ett värde mellan 0 och 1. Sannolikheten att identifieras korrekt språk. Poängsättningen kan vara lägre än 1 om meningen har olika språk.  |

##  <a name="sample-definition"></a>Exempel definition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill ",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Exempel på utdata

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Fel
Om texten uttrycks i ett språk som inte stöds, genereras ett fel och inget språk-ID returneras.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)