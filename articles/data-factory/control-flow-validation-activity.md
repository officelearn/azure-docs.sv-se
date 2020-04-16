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
ms.openlocfilehash: 764b41d1823e8edce134c5099e066486f4f08acc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417937"
---
# <a name="validation-activity-in-azure-data-factory"></a>Valideringsaktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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
namn | Namnet på "Valideringsaktiviteten" | Sträng | Ja |
typ | Måste vara inställt på **Validering**. | Sträng | Ja |
Datamängd | Aktiviteten blockerar körningen tills den har validerat den här datauppsättningsreferensen finns och att den uppfyller de angivna villkoren eller att timeout har nåtts. Den datauppsättning som tillhandahålls bör ha stöd för egenskapen "MinimumSize" eller "ChildItems". | Referens för datauppsättning | Ja |
timeout | Anger tidsgränsen för aktivitetens körning. Om inget värde anges är standardvärdet 7 dagar ("7.00:00:00"). Formatet är d.hh:mm:ss | Sträng | Inga |
Sömn | Fördröjning i sekunder mellan valideringsförsök. Om inget värde anges är standardvärdet 10 sekunder. | Integer | Inga |
childItems | Kontrollerar om mappen har underordnade objekt. Kan ställas in på true: Verifiera att mappen finns och att den har objekt. Blockerar tills minst ett objekt finns i mapp- eller timeout-värdet har uppnåtts.-false: Verifiera att mappen finns och att den är tom. Blockerar tills mappen är tom eller tills timeout-värdet har uppnåtts. Om inget värde anges blockeras aktiviteten tills mappen finns eller tills timeouten har nåtts. | Boolesk | Inga |
minimumSize | Minsta storlek på en fil i byte. Om inget värde anges är standardvärdet 0 byte | Integer | Inga |


## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Köra pipelineaktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Tills-aktivitet](control-flow-until-activity.md)
