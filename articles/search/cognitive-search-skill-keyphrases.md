---
title: Nyckelfras extrahering kognitiv skicklighet
titleSuffix: Azure Cognitive Search
description: Utvärderar ostrukturerad text och returnerar för varje post en lista med nyckelfraser i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791982"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Nyckelfras extrahering kognitiv skicklighet

**Med nyckelfrasex extraktion** utvärderas ostrukturerad text och returnerar för varje post en lista med nyckelfraser. Den här färdigheten använder de maskininlärningsmodeller som tillhandahålls av [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

Den här funktionen är användbar om du snabbt behöver identifiera de viktigaste samtalspunkterna i posten. Till exempel, med tanke på inmatningstexten "Maten var utsökt och det fanns underbar personal", tjänsten returnerar "mat" och "underbar personal".

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken på en post bör vara 50 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)000 tecken mätt med . Om du behöver dela upp dina data innan du skickar dem till nyckelfrasutsugaren kan du använda [textdelningsfärdigheten](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Indata                | Beskrivning |
|---------------------|-------------|
| standardSpråkkod | (Valfritt) Språkkoden som ska tillämpas på dokument som inte uttryckligen anger språk.  Om standardspråkkoden inte anges används engelska (en) som standardspråkkod. <br/> Se [Fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Valfritt) Det maximala antalet nyckelfraser att producera. |

## <a name="skill-inputs"></a>Indata för färdighet

| Indata     | Beskrivning |
|--------------------|-------------|
| text | Texten som ska analyseras.|
| languageCode  |  En sträng som anger posternas språk. Om den här parametern inte anges används standardspråkkoden för att analysera posterna. <br/>Se [Fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Exempeldefinition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
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
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
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
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Fel och varningar
Om du anger en språkkod som inte stöds genereras ett fel och nyckelfraser extraheras inte.
Om texten är tom visas en varning.
Om texten är större än 50 000 tecken analyseras endast de första 50 000 tecknen och en varning utfärdas.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
