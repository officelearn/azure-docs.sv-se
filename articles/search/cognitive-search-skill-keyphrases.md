---
title: Nyckeln frasen extrahering kognitiva Sök kunskaper (Azure Search) | Microsoft Docs
description: Utvärderar Ostrukturerade text och returnerar en lista med viktiga fraser för varje post i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a12efaa020e626e4a10a0708c9b84d8fe125588c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Nyckeln frasen extrahering kognitiva kunskaper

Den **nyckel frasen extrahering** kvalifikationer utvärderar Ostrukturerade text och returnerar en lista med viktiga fraser för varje post.

Den här funktionen är användbart om du behöver att snabbt kunna identifiera de viktigaste aspekterna prata i posten. Till exempel angivna indata ”mat har delikat och det fanns fantastiska personal” tjänsten returnerar ”mat” och ”fantastiska personal”.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken med `String.Length`. Om du vill dela upp dina data innan den skickas till viktiga frasen extraktor bör du använda den [Text dela kunskaper](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaper parametrar

Parametrar är skiftlägeskänsliga.
| Indata                | Beskrivning |
|---------------------|-------------|
| defaultLanguageCode | (Valfritt) Språkkoden som ska användas för dokument som inte uttryckligen anger språk.  Om Standardspråkkod inte är angiven, engelska används (en) som Standardspråkkod. <br/> Se [fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Valfritt) Det maximala antalet viktiga fraser för att skapa. |

## <a name="skill-inputs"></a>Kunskaper indata
| Indata     | Beskrivning |
|--------------------|-------------|
| Text | Texten som ska analyseras.|
| languageCode  |  En sträng som anger språk poster. Om den här parametern inte anges används Standardspråkkod att analysera poster. <br/>Se [fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Exempel definition

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
Om du anger en kod med språket stöds inte genereras ett fel och viktiga fraser extraheras inte.
Om texten är tomt kommer en varning genereras.
Om texten är större än 50 000 tecken, kommer endast de första 50 000 tecknen analyseras och utfärdas en varning.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)