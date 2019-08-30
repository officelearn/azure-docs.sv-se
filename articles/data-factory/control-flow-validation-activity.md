---
title: Validerings aktivitet i Azure Data Factory | Microsoft Docs
description: Validerings aktiviteten fortsätter inte att köra pipelinen förrän den verifierar den bifogade data uppsättningen med vissa villkor som användaren anger.
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
ms.openlocfilehash: 77fdab04e03429d135875cb2ef223e8c23d312a2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141608"
---
# <a name="validation-activity-in-azure-data-factory"></a>Validerings aktivitet i Azure Data Factory
Du kan använda en verifiering i en pipeline för att se till att pipelinen bara fortsätter att köra när den har verifierat att den bifogade data uppsättnings referensen finns, att den uppfyller de angivna villkoren eller att tids gränsen har nåtts.


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


## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
name | Namnet på validerings aktiviteten | Sträng | Ja |
type | Måste vara inställd på **verifiering**. | Sträng | Ja |
data uppsättning | Aktiviteten blockerar körningen tills den har verifierat att den här data uppsättnings referensen finns och att den uppfyller de angivna villkoren, eller också har tids gränsen nåtts. Den angivna data uppsättningen ska ha stöd för egenskapen "MinimumSize" eller "ChildItems". | Referens för data mängd | Ja |
timeout | Anger tidsgränsen för aktivitetens körning. Om inget värde anges är standardvärdet 7 dagar ("7.00:00:00"). Formatet är d. hh: mm: SS | Sträng | Nej |
Spar | En fördröjning i sekunder mellan verifierings försök. Om inget värde anges är standardvärdet 10 sekunder. | Integer | Nej |
childItems | Kontrollerar om mappen har underordnade objekt. Kan anges till-True: Kontrol lera att mappen finns och att den har objekt. Block tills minst ett objekt finns i mappen eller så har timeout-värdet nåtts.-falskt: Kontrol lera att mappen finns och att den är tom. Blockerar tills mappen är tom eller tills timeout-värdet nås. Om inget värde anges blockerar aktiviteten tills mappen finns eller tills tids gränsen har nåtts. | Boolesk | Nej |
minimumSize | Minsta storlek på en fil i byte. Om inget värde anges är standardvärdet 0 byte | Integer | Nej |


## <a name="next-steps"></a>Nästa steg
Se andra kontroll flödes aktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webb aktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
