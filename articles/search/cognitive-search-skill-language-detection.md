---
title: Språk identifiering kognitiv Sök kompetens – Azure Search
description: Utvärderar ostrukturerad text, och för varje post returneras en språk identifierare med poäng som anger styrkan i analysen i en Azure Search anriknings pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 14163b959a6e91406133ca2f5a125c7e2df967ad
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635800"
---
#   <a name="language-detection-cognitive-skill"></a>Kognitiv kompetens för språk identifiering

**Språkidentifiering** -kunskapen identifierar språket för inmatad text och rapporterar en enda språkkod för varje dokument som skickas på begäran. Språk koden är kopplad till en poäng som anger styrkan i analysen. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

Den här funktionen är särskilt användbar när du behöver ange språket för texten som inskickad till andra kunskaper (till exempel [Attitydanalys kompetens](cognitive-search-skill-sentiment.md) eller [text delnings kunskaper](cognitive-search-skill-textsplit.md)).

Språk identifieringen utnyttjar Bing: s naturliga språk bearbetnings bibliotek, vilket överskrider antalet [språk och regioner som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) för textanalys. Den exakta listan över språk publiceras inte, men innehåller alla talade språk, plus varianter, dialekter och vissa regionala och kulturella språk. Om du har innehåll som uttryckts på ett mindre vanligt språk kan du [prova språkidentifiering-API: et](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) för att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `unknown`.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av dokument-cracking-fasen i Azure Search. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan Azure Search priser](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Om du behöver dela upp dina data innan du skickar dem till sentiment Analyzer kan du använda [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Kompetens inmatningar

Parametrar är Skift läges känsliga.

| Indata     | Beskrivning |
|--------------------|-------------|
| text | Den text som ska analyseras.|

## <a name="skill-outputs"></a>Kunskaps utmatningar

| Namn på utdata    | Beskrivning |
|--------------------|-------------|
| languageCode | ISO 6391-språkkoden för det språk som identifierades. Till exempel "en". |
| languageName | Språkets namn. Till exempel "engelska". |
| poäng | Ett värde mellan 0 och 1. Sannolikheten att språket identifieras korrekt. Poängen kan vara lägre än 1 om meningen har blandade språk.  |

##  <a name="sample-definition"></a>Exempel definition

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

##  <a name="sample-input"></a>Exempel på inmatade

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


## <a name="error-cases"></a>Fel fall
Om texten uttrycks i ett språk som inte stöds, genereras ett fel och ingen språk identifierare returneras.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
