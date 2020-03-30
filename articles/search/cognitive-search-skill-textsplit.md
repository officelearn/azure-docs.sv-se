---
title: Text delad kognitiv färdighet
titleSuffix: Azure Cognitive Search
description: Dela upp text i segment eller sidor med text baserat på längd i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3f80169808b1e6420f04b786d2bb06bde9c96231
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73479668"
---
# <a name="text-split-cognitive-skill"></a>Text delad kognitiv färdighet

Textdelningsfärdigheten delar upp text i textsegment. **Text Split** Du kan ange om du vill dela upp texten i meningar eller till sidor av en viss längd. Den här färdigheten är särskilt användbar om det finns maximala krav på textlängd i andra kunskaper nedströms. 

> [!NOTE]
> Den här färdigheten är inte bunden till ett Cognitive Services API och du debiteras inte för att använda den. Du bör fortfarande [bifoga en Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md)för att åsidosätta alternativet **Kostnadsfri** resurs som begränsar dig till ett litet antal dagliga berikningar per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.splitskill 

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| textSplitMode      | Antingen "sidor" eller "meningar" | 
| maximumPageLength | Om textSplitMode är inställd på "sidor", refererar detta `String.Length`till den maximala sidlängden mätt med . Minimivärdet är 100.  Om textenSplitMode är inställd på "sidor" kommer algoritmen att försöka dela upp texten i segment som högst är "maximumPageLength" i storlek. I det här fallet gör algoritmen sitt bästa för att bryta meningen på en meningsgräns, så storleken på segmentet kan vara något mindre än "maximumPageLength". | 
| standardSpråkkod   | (valfritt) En av följande språkkoder: `da, de, en, es, fi, fr, it, ko, pt`. Standard är engelska (sv). Få saker att tänka på:<ul><li>Om du skickar ett språkkodslandkodformat används bara språkkodsdelen i formatet.</li><li>Om språket inte finns i föregående lista bryter den delade färdigheten texten vid teckengränser.</li><li>Att tillhandahålla en språkkod är användbart för att undvika att klippa ett ord på mitten för icke-blanktecken som kinesiska, japanska och koreanska.</li><li>Om du inte kan språket (dvs. du måste dela upp texten för inmatning i [LanguageDetectionSkill),](cognitive-search-skill-language-detection.md)bör standardvärdet för engelska (sv) vara tillräckligt. </li></ul>  |


## <a name="skill-inputs"></a>Indata för färdighet

| Parameternamn       | Beskrivning      |
|----------------------|------------------|
| text  | Texten som ska delas upp i delsträng. |
| languageCode  | (Valfritt) Språkkod för dokumentet. Om du inte kan språket (dvs. du måste dela upp texten för inmatning i [LanguageDetectionSkill),](cognitive-search-skill-language-detection.md)är det säkert att ta bort den här indata.  |

## <a name="skill-outputs"></a>Utdata för färdighet 

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| textObjekt | En matris med delsträngar som extraherades. |


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

##  <a name="sample-input"></a>Exempel på indata

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
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

## <a name="error-cases"></a>Felfall
Om ett språk inte stöds genereras en varning och texten delas upp vid teckengränserna.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
