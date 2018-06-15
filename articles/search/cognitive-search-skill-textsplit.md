---
title: Text dela kognitiva Sök kunskaper (Azure Search) | Microsoft Docs
description: Dela upp text i segment eller sidor med text baserat på längden i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: dbb9261cfce0a8437cfe76121fa16aa87c4b3393
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791030"
---
#   <a name="text-split-cognitive-skill"></a>Dela kognitiva kunskaper text

Den **Text dela** kunskaper delar upp text i mängder text. Du kan ange om du vill dela texten i meningar eller sidor i en viss längd. Kompetensen är särskilt användbart om det finns största längd krav i andra kunskaper nedströms. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Kunskaper parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| textSplitMode      | ”Sidor” eller ”meningarna” | 
| maximumPageLength | Om textSplitMode har angetts till ”sidor” refererar detta till största längd mätt efter `String.Length`. Det lägsta värdet är 100. | 
| defaultLanguageCode   | (valfritt) En av följande språkkoder: `da, de, en, es, fi, fr, it, ko, pt`. Standardvärdet är engelska (en). Några saker att tänka på:<ul><li>Om du skickar ett languagecode countrycode format används languagecode del av formatet.</li><li>Om språket inte är i listan ovan bryts dela kompetensen texten vid tecknet gränser.</li><li>Att tillhandahålla en språkkod är användbar för att undvika att klippa ut ett ord i hälften för språk som inte är blanksteg, till exempel kinesiska, japanska och koreanska.</li></ul>  |


## <a name="skill-inputs"></a>Kunskaper indata

| Parameternamn       | Beskrivning      |
|----------------------|------------------|
| Text  | Texten som ska delas upp i delsträngen. |
| languageCode  | (Valfritt) Språkkod för dokumentet.  |

## <a name="skill-outputs"></a>Kunskaper utdata 

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| textItems | En matris med delsträngar extraherades. |


##  <a name="sample-definition"></a>Exempel definition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
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
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Exempel på utdata

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Fel
Om ett språk inte stöds, en varning genereras och texten delas med tecknet gränser.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
