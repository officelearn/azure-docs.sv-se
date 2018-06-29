---
title: Filtrera aktiviteten i Azure Data Factory | Microsoft Docs
description: Aktiviteten Filter filtrerar indata.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: shlo
ms.openlocfilehash: b3b26869a84b8519ced19a4c93a6d39d6ed20f9b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050360"
---
# <a name="filter-activity-in-azure-data-factory"></a>Filtrera aktiviteten i Azure Data Factory
Du kan använda en Filter-aktivitet i en pipeline för att tillämpa ett filteruttryck till en matris som indata. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Typegenskaper

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på den `Filter` aktivitet. | Sträng | Ja
typ | Måste anges till **filter**. | Sträng | Ja
tillstånd | Villkoret som ska användas för att filtrera indata. | Uttryck | Ja
objekt | Indatamatrisen som filtret ska tillämpas. | Uttryck | Ja

## <a name="example"></a>Exempel

I det här exemplet har två aktiviteter i pipeline: **Filter** och **ForEach**. Aktiviteten Filter har konfigurerats för att filtrera Indatamatrisen för artiklar med ett större värde än 3. ForEach-aktiviteten sedan itererar över de filtrerade värdena och väntar på att antalet sekunder som anges av det aktuella värdet.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
                "name": "MyForEach",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "batchCount": 1,
                    "items": "@activity('MyFilterActivity').output.value",
                    "activities": [{
                        "type": "Wait",
                        "typeProperties": {
                            "waitTimeInSeconds": "@item()"
                        },
                        "name": "MyWaitActivity"
                    }]
                },
                "dependsOn": [{
                    "activity": "MyFilterActivity",
                    "dependencyConditions": ["Succeeded"]
                }]
            }
        ],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
Se annan kontrollflödesaktiviteter som stöds av Data Factory: 

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
