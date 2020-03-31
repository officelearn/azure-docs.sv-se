---
title: Valideringsaktivitet i Azure Data Factory
description: Valideringsaktiviteten fortsätter inte körningen av pipelinen förrän den validerar den bifogade datauppsättningen med vissa kriterier som användaren anger.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: f63c78c59d7d6be3c66ea0785389eff73e3bff60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678370"
---
# <a name="validation-activity-in-azure-data-factory"></a>Valideringsaktivitet i Azure Data Factory
Du kan använda en validering i en pipeline för att säkerställa att pipelinen bara fortsätter körningen när den har validerat den bifogade datauppsättningsreferensen finns, att den uppfyller de angivna villkoren eller att timeout har nåtts.


## <a name="syntax"></a>Syntax

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Egenskaper för typ

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på "Valideringsaktiviteten" | String | Ja |
typ | Måste vara inställt på **Validering**. | String | Ja |
Datamängd | Aktiviteten blockerar körningen tills den har validerat den här datauppsättningsreferensen finns och att den uppfyller de angivna villkoren eller att timeout har nåtts. Den datauppsättning som tillhandahålls bör ha stöd för egenskapen "MinimumSize" eller "ChildItems". | Referens för datauppsättning | Ja |
timeout | Anger tidsgränsen för aktivitetens körning. Om inget värde anges är standardvärdet 7 dagar ("7.00:00:00"). Formatet är d.hh:mm:ss | String | Inga |
Sömn | Fördröjning i sekunder mellan valideringsförsök. Om inget värde anges är standardvärdet 10 sekunder. | Integer | Inga |
childItems | Kontrollerar om mappen har underordnade objekt. Kan ställas in på true: Verifiera att mappen finns och att den har objekt. Blockerar tills minst ett objekt finns i mapp- eller timeout-värdet har uppnåtts.-false: Verifiera att mappen finns och att den är tom. Blockerar tills mappen är tom eller tills timeout-värdet har uppnåtts. Om inget värde anges blockeras aktiviteten tills mappen finns eller tills timeouten har nåtts. | Boolean | Inga |
minimumSize | Minsta storlek på en fil i byte. Om inget värde anges är standardvärdet 0 byte | Integer | Inga |


## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
