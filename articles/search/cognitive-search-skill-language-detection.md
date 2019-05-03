---
title: Språk-kunskaper för kognitiv sökning av identifiering – Azure Search
description: Utvärderar ostrukturerad text och returnerar ett språk-ID för varje post med ett värde som anger styrkan hos analyser i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a076eee9818f294a8e5c4b10cebbcb9e5a55d80c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021854"
---
#   <a name="language-detection-cognitive-skill"></a>Språk identifiering kognitiva kunskaper

Den **språkidentifiering** färdighet identifierar språket i indatatexten och rapporterar en enda språkkod för varje dokument som skickats på begäran. Språkkoden är kopplad till ett värde som anger styrkan hos analysen. Kompetensen använder maskininlärningsmodeller som tillhandahålls av [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

Den här funktionen är särskilt användbar när du ska ange språket för texten som indata för andra färdigheter (till exempel den [Attitydanalys färdighet](cognitive-search-skill-sentiment.md) eller [Text dela färdighet](cognitive-search-skill-textsplit.md)).

Språkidentifiering utnyttjar Bings bibliotek för bearbetning av naturligt språk, vilket överskrider antalet av [språk och regioner som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) för textanalys. Den exakta listan över språk har inte publicerats, men innehåller alla omfattning talade språk, plus varianter, dialekter och vissa regional och kulturell språk. Om du har innehåll som uttrycks i ett språk som används mindre ofta kan du [prova detta API för identifiering av språket](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `unknown`.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, att lägga till fler dokument eller att lägga till fler AI-algoritmer, måste du [bifoga en fakturerbar resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md). Avgifter tillkommer när du anropar API: er i Cognitive Services och extrahering av avbildningen som en del av det dokumentknäckning steget i Azure Search. Det finns inga avgifter för textextrahering från dokument.
>
> Körningen av inbyggda färdigheter som ingår debiteras enligt den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Bild extrahering priser beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).


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
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
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
