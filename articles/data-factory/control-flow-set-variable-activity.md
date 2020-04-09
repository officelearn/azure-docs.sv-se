---
title: Ange variabel aktivitet i Azure Data Factory
description: Lär dig hur du använder aktiviteten Ange variabel för att ange värdet för en befintlig variabel som definierats i en Data Factory-pipeline
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
ms.openlocfilehash: e736cc95628bd0e15bdb7ffd425608278788c353
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879281"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ange variabel aktivitet i Azure Data Factory

Använd aktiviteten Ange variabel för att ange värdet för en befintlig variabel av typen Sträng, Bool eller Matris som definierats i en Data Factory-pipeline.

## <a name="type-properties"></a>Egenskaper för typ

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
namn | Namnet på den på gång som den verksamhet som är i gång | ja
description | Text som beskriver vad aktiviteten gör | nej
typ | Måste ställas in **på SetVariable** | ja
värde | Stränglitteralt eller uttrycksobjektvärde som variabeln ska tilldelas till | ja
variableName (variabelNamn) | Namnet på den variabel som ska ställas in av den här aktiviteten | ja

## <a name="incrementing-a-variable"></a>Öka en variabel

Ett vanligt scenario med variabler i Azure Data Factory använder en variabel som iterator inom en till- eller förförd aktivitet. I en uppsättningsvariabelaktivitet kan du `value` inte referera till variabeln som anges i fältet. Om du vill lösa den här begränsningen anger du en tillfällig variabel och skapar sedan en andra uppsättningsvariabelaktivitet. Den andra insatta variabelaktiviteten anger iteratorns värde till den tillfälliga variabeln. 

Nedan följer ett exempel på detta mönster:

![Variabel för ökning](media/control-flow-set-variable-activity/increment-variable.png "Variabel för ökning")

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
Lär dig mer om en relaterad kontrollflödesaktivitet som stöds av Data Factory: 

- [Lägga till variabelaktivitet](control-flow-append-variable-activity.md)
