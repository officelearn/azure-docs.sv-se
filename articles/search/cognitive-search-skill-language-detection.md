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
ms.openlocfilehash: 2fd1c1ec0d2442afd6367e1d35af6f798dced2c7
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733286"
---
#   <a name="language-detection-cognitive-skill"></a>Språk identifiering kognitiva kunskaper

Upp till 120 språk som den **språkidentifiering** färdighet identifierar språket i indatatexten och rapporterar en enda språkkod för varje dokument som skickats på begäran. Språkkoden är kopplad till ett värde som anger styrkan hos analysen.

Den här funktionen är särskilt användbar när du ska ange språket för texten som indata för andra färdigheter (till exempel den [Attitydanalys färdighet](cognitive-search-skill-sentiment.md) eller [Text dela färdighet](cognitive-search-skill-textsplit.md)).

> [!NOTE]
> Kognitiv sökning är tillgängligt som en förhandsversion. Kompetens körning och extrahering av avbildningen och normalisering är för närvarande erbjuds kostnadsfritt. Vid ett senare tillfälle meddelas priserna för dessa funktioner. 

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
