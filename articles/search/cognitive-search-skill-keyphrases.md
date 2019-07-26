---
title: Nyckel fras extrahering inlärning Sök kunskaper – Azure Search
description: Utvärderar ostrukturerad text och returnerar en lista med nyckel fraser i en Azure Search anriknings pipeline för varje post.
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
ms.openlocfilehash: 87f20f1989c70988784b422fc38094ec1fc5a966
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347727"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Extrahering av diskussionsämne kognitiva kunskaper

Den **extrahering av diskussionsämnea** kunskapen utvärderar ostrukturerad text och returnerar en lista över viktiga fraser för varje post. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

Den här funktionen är användbar om du snabbt behöver identifiera de viktigaste pratar punkterna i posten. Till exempel, den inmatade texten "livsmedlet var Delicious och det var en fantastiskt anställd", returnerar tjänsten "mat" och "trevlig personal".

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av dokument-cracking-fasen i Azure Search. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan Azure Search priser](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Om du behöver dela upp dina data innan du skickar dem till nyckel frasen Extractor, bör du överväga att använda [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga.

| Indata                | Beskrivning |
|---------------------|-------------|
| defaultLanguageCode | Valfritt Språk koden som ska användas för dokument som inte uttryckligen anger språk.  Om standard språk koden inte anges används engelska (en) som standard språk kod. <br/> Se en [fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | Valfritt Det maximala antalet nyckel fraser som ska skapas. |

## <a name="skill-inputs"></a>Kompetens inmatningar

| Indata     | Beskrivning |
|--------------------|-------------|
| text | Den text som ska analyseras.|
| languageCode  |  En sträng som anger språket för posterna. Om den här parametern inte anges används standard språk koden för att analysera posterna. <br/>Se en [fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

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

##  <a name="sample-input"></a>Exempel på inmatade

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
Om du anger en språk kod som inte stöds genereras ett fel och nyckel fraser extraheras inte.
Om texten är tom skapas en varning.
Om texten är större än 50 000 tecken kommer endast de första 50 000 tecknen att analyseras och en varning utfärdas.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
