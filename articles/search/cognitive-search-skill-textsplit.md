---
title: Text delning kognitiv Sök kompetens – Azure Search
description: Dela upp text i segment eller sidor med text baserat på längd i en Azure Search anriknings pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 8fb7ff51507212dfb162c09fcee469d6f154f3c3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840897"
---
#   <a name="text-split-cognitive-skill"></a>Text delning kognitiv kunskap

**Texten Split** -kompetensen avbryter text i segment med text. Du kan ange om du vill dela upp texten i meningar eller på sidor med en viss längd. Den här kunskapen är särskilt användbar om det finns maximalt text längds krav i andra färdigheter. 

> [!NOTE]
> Den här kunskapen är inte kopplad till ett Cognitive Services-API och du debiteras inte för att använda den. Du bör fortfarande [bifoga en Cognitive Services resurs](cognitive-search-attach-cognitive-services.md), men för att åsidosätta det **kostnads fria** resurs alternativet som begränsar dig till ett litet antal dagliga berikare per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. SplitSkill 

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| textSplitMode      | Antingen "Pages" eller "meningar" | 
| maximumPageLength | Om textSplitMode är inställt på "Pages", refererar detta till den maximala sid längden `String.Length`som mäts av. Minimivärdet är 100.  Om textSplitMode är inställt på "Pages" försöker algoritmen dela upp texten i segment som har flest "maximumPageLength" i storlek. I det här fallet gör algoritmen det bästa sättet att dela upp meningen på en mening, så storleken på segmentet kan vara något lägre än "maximumPageLength". | 
| defaultLanguageCode   | valfritt En av följande språk koder: `da, de, en, es, fi, fr, it, ko, pt`. Standardvärdet är engelska (en). Några saker att tänka på:<ul><li>Om du skickar ett languageCode-CountryCode-format används endast languageCode-delen av formatet.</li><li>Om språket inte finns i den föregående listan, delar den delade kompetensen texten vid tecken gränser.</li><li>Att ange en språkkod är användbart för att undvika att klippa ut ett ord på hälften för icke-utrymmes språk, till exempel kinesiska, japanska och koreanska.</li></ul>  |


## <a name="skill-inputs"></a>Kompetens inmatningar

| Parameternamn       | Beskrivning      |
|----------------------|------------------|
| text  | Den text som ska delas upp i under sträng. |
| languageCode  | Valfritt Språk kod för dokumentet.  |

## <a name="skill-outputs"></a>Kunskaps utmatningar 

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| textItems | En matris med del strängar som har extraherats. |


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

##  <a name="sample-input"></a>Exempel på inmatade

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

## <a name="error-cases"></a>Fel fall
Om ett språk inte stöds genereras en varning och texten delas upp vid tecken gränser.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
