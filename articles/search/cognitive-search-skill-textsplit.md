---
title: Text dela kognitiv sökning färdigheter – Azure Search
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
ms.custom: seodec2018
ms.openlocfilehash: 376a5a2214c5f1d77439923d6a11aaad5e011e73
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313861"
---
#   <a name="text-split-cognitive-skill"></a>Text dela kognitiva kunskaper

Den **Text dela** färdighet delar upp text i segment med text. Du kan ange om du vill dela texten i meningar eller sidor i en viss längd. Kompetensen är särskilt användbart om det finns maximala text längdkraven i andra färdigheter nedströms. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Färdighet parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| textSplitMode      | ”Sidor” eller ”meningar” | 
| maximumPageLength | Om textSplitMode anges till ”sidor”, avser den maximala sidlängden enligt `String.Length`. Det lägsta värdet är 100.  Om textSplitMode har angetts till ”sidor” försöker algoritmen att dela upp texten i segment som innehåller högst ”maximumPageLenth” i storlek. I det här fallet fungerar algoritmen som bra att bryta meningen på en mening gräns så att storleken på segmentet kan vara något mindre än ”maximumPageLength”. | 
| defaultLanguageCode   | (valfritt) En av följande språkkoder: `da, de, en, es, fi, fr, it, ko, pt`. Standardvärdet är engelska (en). Några saker att tänka på:<ul><li>Om du skickar ett languagecode countrycode format används bara den languagecode delen av formatet.</li><li>Om språket inte är i listan ovan, delar dela färdighet texten med tecknet-gränser.</li><li>Att tillhandahålla en språkkod är användbar för att undvika att klippa ut ett ord i hälften för språk som inte är blanksteg, till exempel kinesiska, japanska och koreanska.</li></ul>  |


## <a name="skill-inputs"></a>Färdighet indata

| Parameternamn       | Beskrivning      |
|----------------------|------------------|
| text  | Texten att dela upp i delsträngen. |
| languageCode  | (Valfritt) Språkkod för dokumentet.  |

## <a name="skill-outputs"></a>Färdighet utdata 

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| textItems | En matris med delsträngar som extraherades. |


##  <a name="sample-definition"></a>Exempeldefinition

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

## <a name="error-cases"></a>Felhändelser
Om ett språk inte stöds, en varning genereras och texten är uppdelade vid tecknet gränser.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
