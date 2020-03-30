---
title: Språkidentifiering kognitiv skicklighet
titleSuffix: Azure Cognitive Search
description: Utvärderar ostrukturerad text och returnerar för varje post en språkidentifierare med en poäng som anger analysens styrka i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8439788c63ec1b9feaea148ab52aba498791dc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045015"
---
#   <a name="language-detection-cognitive-skill"></a>Språkidentifiering kognitiv skicklighet

**Språkidentifieringsfärdigheten** identifierar språket för indatatext och rapporterar en enda språkkod för varje dokument som skickas på begäran. Språkkoden paras ihop med en poäng som anger analysens styrka. Den här färdigheten använder de maskininlärningsmodeller som tillhandahålls av [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

Den här funktionen är särskilt användbar när du behöver ange språket i texten som indata till andra färdigheter (till exempel [sentimentanalysfärdigheten](cognitive-search-skill-sentiment.md) eller [textdelningsfärdigheten).](cognitive-search-skill-textsplit.md)

Språkidentifiering utnyttjar Bings bibliotek för bearbetning av naturligt språk, vilket överstiger antalet [språk och regioner som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) för Textanalys. Den exakta listan över språk publiceras inte, utan omfattar alla allmänt talade språk, plus varianter, dialekter och vissa regionala och kulturella språk. Om du har innehåll som uttrycks på ett språk som används mindre ofta kan du [prova API:et för språkidentifiering](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) för att se om det returnerar en kod. Svaret för språk som inte `unknown`kan identifieras är .

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken på en post bör vara 50 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)000 tecken mätt med . Om du behöver dela upp dina data innan du skickar dem till språkidentifieringsfärdigheten kan du använda [läskunskapen Text split](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Indata för färdighet

Parametrar är skiftlägeskänsliga.

| Indata     | Beskrivning |
|--------------------|-------------|
| text | Texten som ska analyseras.|

## <a name="skill-outputs"></a>Utdata för färdighet

| Utdatanamn    | Beskrivning |
|--------------------|-------------|
| languageCode | Iso 6391 språkkod för det språk som identifierats. Till exempel "en". |
| språkNamn | Språkets namn. Till exempel "engelska". |
| poäng | Ett värde mellan 0 och 1. Sannolikheten för att språket identifieras korrekt. Poängen kan vara lägre än 1 om meningen har blandade språk.  |

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

##  <a name="sample-input"></a>Exempelinmatning

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


## <a name="error-cases"></a>Felfall
Om texten uttrycks på ett språk som inte stöds genereras ett fel och ingen språkidentifierare returneras.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
