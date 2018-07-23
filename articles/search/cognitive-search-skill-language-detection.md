---
title: Språk identifiering kognitiv sökning färdighet (Azure Search) | Microsoft Docs
description: Utvärderar ostrukturerad text och returnerar ett språk-ID för varje post med ett värde som anger styrkan hos analyser i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 443ac895085053b7c4c876c3deecaa1943c9f506
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171066"
---
#   <a name="language-detection-cognitive-skill"></a>Språk identifiering kognitiva kunskaper

Upp till 120 språk som den **språkidentifiering** färdighet identifierar språket i indatatexten och rapporterar en enda språkkod för varje dokument som skickats på begäran. Språkkoden är kopplad till ett värde som anger styrkan hos analysen.

Den här funktionen är särskilt användbar när du ska ange språket för texten som indata för andra färdigheter (till exempel den [Attitydanalys färdighet](cognitive-search-skill-sentiment.md) eller [Text dela färdighet](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken enligt `String.Length`. Om du vill dela upp dina data innan de skickas till sentiment analyzer kan du använda den [Text dela färdighet](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Färdighet indata

Parametrar är skiftlägeskänsliga.

| Indata     | Beskrivning |
|--------------------|-------------|
| text | Texten som ska analyseras.|

## <a name="skill-outputs"></a>Färdighet utdata

| Namn på utdata    | Beskrivning |
|--------------------|-------------|
| languageCode | ISO 6391 språkkoden för språket som identifieras. Till exempel ”SV”. |
| LanguageName | Namnet på språket. Till exempel ”engelska”. |
| poäng | Ett värde mellan 0 och 1. Sannolikheten att identifieras korrekt språk. Poängen kan vara lägre än 1 om meningen har olika språk.  |

##  <a name="sample-definition"></a>Exempeldefinition

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


## <a name="error-cases"></a>Felhändelser
Om texten uttrycks i en språket stöds inte, genereras ett fel och inget språk-ID returneras.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
