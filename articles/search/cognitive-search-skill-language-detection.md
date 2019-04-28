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
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 605f4c639cfc8c0f9732f7347532e1bd7edc055f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341640"
---
#   <a name="language-detection-cognitive-skill"></a>Språk identifiering kognitiva kunskaper

Den **språkidentifiering** färdighet identifierar språket i indatatexten och rapporterar en enda språkkod för varje dokument som skickats på begäran. Språkkoden är kopplad till ett värde som anger styrkan hos analysen. Kompetensen använder maskininlärningsmodeller som tillhandahålls av [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

Den här funktionen är särskilt användbar när du ska ange språket för texten som indata för andra färdigheter (till exempel den [Attitydanalys färdighet](cognitive-search-skill-sentiment.md) eller [Text dela färdighet](cognitive-search-skill-textsplit.md)).

Språkidentifiering utnyttjar Bings bibliotek för bearbetning av naturligt språk, vilket överskrider antalet av [språk och regioner som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) för textanalys. Den exakta listan över språk har inte publicerats, men innehåller alla omfattning talade språk, plus varianter, dialekter och vissa regional och kulturell språk. Om du har innehåll som uttrycks i ett språk som används mindre ofta kan du [prova detta API för identifiering av språket](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `unknown`.

> [!NOTE]
> Från och med den 21 December 2018 kan du [bifoga en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md) med en Azure Search-kompetens. På så sätt kan vi börjar debitera för körning av kompetens. På det här datumet måste också började vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.
>
> [Inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) körning som ingår debiteras enligt de [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services), på samma pris som om du har utfört uppgiften direkt. Extrahering av avbildningen är en Azure Search-avgift kan för närvarande på priset för förhandsversionen. Mer information finns i [Azure Search prissättningssidan](https://go.microsoft.com/fwlink/?linkid=2042400) eller [hur debiteringen fungerar](search-sku-tier.md#how-billing-works).

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
