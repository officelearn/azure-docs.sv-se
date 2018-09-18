---
title: Nyckeln frasen extrahering kognitiv sökning färdighet (Azure Search) | Microsoft Docs
description: Utvärderar ostrukturerad text och returnerar en lista med viktiga fraser för varje post i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 694271115626c652523be34160ad6a07053f6387
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735806"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Nyckeln diskussionsämne kognitiva kunskaper

Den **nyckel diskussionsämne** färdigheter utvärderar ostrukturerad text och returnerar en lista med viktiga fraser för varje post.

Den här funktionen är användbar om du behöver att snabbt identifiera de viktigaste samtalsämnen i posten. Till exempel angivna indata-text ”livsmedelskedjan var härligt och det fanns underbar personal”, tjänsten returnerar ”mat” och ”underbar personal”.

> [!NOTE]
> Kognitiv sökning är tillgängligt som en förhandsversion. Kompetens körning och extrahering av avbildningen och normalisering är för närvarande erbjuds kostnadsfritt. Vid ett senare tillfälle meddelas priserna för dessa funktioner. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken enligt `String.Length`. Om du vill dela upp dina data innan de skickas till diskussionsämne extraktor kan du använda den [Text dela färdighet](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Färdighet parametrar

Parametrar är skiftlägeskänsliga.
| Indata                | Beskrivning |
|---------------------|-------------|
| defaultLanguageCode | (Valfritt) Språkkoden som ska användas för dokument som inte uttryckligen anger språk.  Om Standardspråkkod inte är angivna, engelska ska (en) användas som Standardspråkkod. <br/> Se [fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Valfritt) Det maximala antalet viktiga fraser för att producera. |

## <a name="skill-inputs"></a>Färdighet indata
| Indata     | Beskrivning |
|--------------------|-------------|
| text | Texten som ska analyseras.|
| languageCode  |  En sträng som anger språket poster. Om den här parametern inte anges används Standard-språkkod att analysera poster. <br/>Se [fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Exempeldefinition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
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

##  <a name="sample-input"></a>Exempelindata

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
Om du anger ett som inte stöds språkkod kan genereras ett fel och är inte att extrahera nyckelfraser.
Om texten är tom, skapas en varning.
Om texten är längre än 50 000 tecken, endast de första 50 000 tecken som ska analyseras och kommer att utfärdas en varning.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)