---
title: Föråldrade kognitiva färdigheter – Azure Search
description: Den här sidan innehåller en lista med kunskaper om kognitiva sökningar som betraktas som inaktuella och som inte stöds inom en snar framtid.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 1e78852ec8b92f1a9e37a4dbcbbcb371c0ac0f97
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265443"
---
# <a name="deprecated-cognitive-search-skills"></a>Föråldrade kognitiva Sök kunskaper

Det här dokumentet beskriver kognitiva kunskaper som betraktas som föråldrade. Använd följande guide för innehållet:

* Kunskaps namn: Namnet på den kunskap som ska vara inaktuell, mappas till @odata.type attributet.
* Senaste tillgängliga API-version: Den senaste versionen av Azure Searchs offentliga API genom vilka färdighetsuppsättningar som innehåller motsvarande föråldrade färdighet kan skapas/uppdateras.
* Supportens slut: Den sista dagen efter vilken motsvarande färdighet betraktas som ej stöds. Tidigare skapade färdighetsuppsättningar bör fortfarande fortsätta att fungera, men användarna rekommenderas att migrera bort från en föråldrad färdighet.
* Rekommenderade Sökväg för migrering framåt för att använda en färdighet som stöds. Användarna uppmanas att följa rekommendationerna för att fortsätta att få support.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Senaste tillgängliga API-version

2017-11-11-Preview

### <a name="end-of-support"></a>Slut på support

15 februari 2019

### <a name="recommendations"></a>Rekommendationer 

Använd [Microsoft. skicklighets. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) i stället. Den innehåller de flesta funktioner i NamedEntityRecognitionSkill med högre kvalitet. Den innehåller också utförlig information i komplexa utdatakolumner.

Om du vill migrera till [kunskap om enhets igenkänning](cognitive-search-skill-entity-recognition.md)måste du utföra en eller flera av följande ändringar i din kunskaps definition. Du kan uppdatera kunskaps definitionen med [Update färdigheter-API: et](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> För närvarande stöds inte säkerhets Poäng som ett begrepp. `minimumPrecision` Parametern finns`EntityRecognitionSkill` i för framtida användning och bakåtkompatibilitet.

1. *(Krävs)* `@odata.type` Ändra från `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` till. `"#Microsoft.Skills.Text.EntityRecognitionSkill"`

2. *(Valfritt)* Om du använder `entities` utdata använder du i `EntityRecognitionSkill` stället det `namedEntities` komplexa samlings resultatet. Du kan använda `targetName` i kunskaps definitionen för att mappa den till en anteckning som kallas `entities`.

3. *(Valfritt)* Om du inte uttryckligen anger `categories` `EntityRecognitionSkill` , kan returnera olika typer av kategorier förutom de `NamedEntityRecognitionSkill`som stöds av. Om det här beteendet är oönskat, måste du uttryckligen `categories` ange parametern `["Person", "Location", "Organization"]`till.

    _Exempel på migrations definitioner_

    * Enkel migrering

        _Framför NamedEntityRecognition kompetens definition_
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
        _När EntityRecognition kompetens definition_
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
    
    * En något komplicerad migrering

        _Framför NamedEntityRecognition kompetens definition_
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
        _När EntityRecognition kompetens definition_
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

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Kompetens för enhets igenkänning](cognitive-search-skill-entity-recognition.md)
