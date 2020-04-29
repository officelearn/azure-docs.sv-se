---
title: Ange variabel aktivitet i Azure Data Factory
description: Lär dig hur du använder aktiviteten ange variabel för att ange värdet för en befintlig variabel som definierats i en Data Factory pipeline
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e5bd3d10e4e43daf3031aae5083ee917cfe65ede
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417988"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ange variabel aktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd aktiviteten ange variabel för att ange värdet för en befintlig variabel av typen sträng, bool eller matris som definierats i en Data Factory pipeline.

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
name | Namn på aktiviteten i pipeline | ja
description | Text som beskriver vad aktiviteten gör | nej
typ | Måste anges till **SetVariable** | ja
värde | Det sträng tecken eller uttrycks objekt värde som variabeln ska tilldelas | ja
variableName | Namnet på den variabel som ska anges av den här aktiviteten | ja

## <a name="incrementing-a-variable"></a>Stegvis ökning av en variabel

Ett vanligt scenario som involverar variabler i Azure Data Factory är att använda en variabel som en iterator i en till-eller-aktivitet. I en uppsättning variabel aktivitet kan du inte referera till variabeln som anges `value` i fältet. Du kan lösa den här begränsningen genom att ange en tillfällig variabel och sedan skapa en andra uppsättnings variabel aktivitet. Den andra uppsättningen variabel aktivitet anger värdet för Iteratorn till den tillfälliga variabeln. 

Nedan visas ett exempel på det här mönstret:

![Increment-variabel](media/control-flow-set-variable-activity/increment-variable.png "Increment-variabel")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Nästa steg
Lär dig mer om en relaterad kontroll flödes aktivitet som stöds av Data Factory: 

- [Lägga till variabelaktivitet](control-flow-append-variable-activity.md)
