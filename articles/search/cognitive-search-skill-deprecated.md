---
title: Inaktuella kognitiva färdigheter
titleSuffix: Azure Cognitive Search
description: Den här sidan innehåller en lista över kognitiva kunskaper som anses vara inaktuella och som inte kommer att stödjas inom en snar framtid i Azure Cognitive Search-kunskaper.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792025"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Inaktuella kognitiva färdigheter i Azure Cognitive Search

Det här dokumentet beskriver kognitiva färdigheter som anses vara inaktuella. Använd följande guide för innehållet:

* Färdighetsnamn: Namnet på den färdighet som ska inaktuella, den @odata.type mappar till attributet.
* Senast tillgänglig api-version: Den senaste versionen av det offentliga API:et för Azure Cognitive Search genom vilken skillsets som innehåller motsvarande inaktuella färdighet kan skapas/uppdateras.
* Supportslut: Den sista dagen efter vilken motsvarande färdighet inte stöds. Tidigare skapade skillsets bör fortfarande fortsätta att fungera, men användarna rekommenderas att migrera bort från en föråldrad färdighet.
* Rekommendationer: Migreringsvägen framåt för att använda en färdighet som stöds. Användare uppmanas att följa rekommendationerna för att fortsätta att få support.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Senast tillgänglig api-version

2017-11-11-Preview

### <a name="end-of-support"></a>på supporten

15 februari 2019

### <a name="recommendations"></a>Rekommendationer 

Använd [Microsoft.skills.text.entityRecognitionSkill](cognitive-search-skill-entity-recognition.md) i stället. Det ger de flesta av funktionerna i NamedEntityRecognitionSkill på en högre kvalitet. Den har också rikare information i sina komplexa utdatafält.

Om du vill migrera till [entitetsigenkänningsfärdigheten](cognitive-search-skill-entity-recognition.md)måste du utföra en eller flera av följande ändringar i din färdighetsdefinition. Du kan uppdatera färdighetsdefinitionen med hjälp av [API:et för uppdateringskunskaper](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> För närvarande stöds inte förtroendepoäng som koncept. Parametern `minimumPrecision` finns `EntityRecognitionSkill` på för framtida användning och för bakåtkompatibilitet.

1. *(Obligatoriskt)* Ändra `@odata.type` från `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` `"#Microsoft.Skills.Text.EntityRecognitionSkill"`till .

2. *(Valfritt)* Om du använder `entities` utdata använder `namedEntities` du den komplexa `EntityRecognitionSkill` insamlingsutdata från i stället. Du kan `targetName` använda i färdighetsdefinitionen för att `entities`mappa den till en anteckning som kallas .

3. *(Valfritt)* Om du inte uttryckligen `categories`anger `EntityRecognitionSkill` kan du returnera olika typer av `NamedEntityRecognitionSkill`kategorier än de som stöds av . Om det här beteendet inte är önskvärt, `categories` se `["Person", "Location", "Organization"]`till att uttryckligen ange parametern till .

    _Exempel på migreringsdefinitioner_

    * Enkel migrering

        _(Före) Definition av egenskapenEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(Efter) Skill definition av enhetRekognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Något komplicerad migration

        _(Före) Definition av egenskapenEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(Efter) Skill definition av enhetRekognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Färdighet för entitetsigenkän](cognitive-search-skill-entity-recognition.md)
