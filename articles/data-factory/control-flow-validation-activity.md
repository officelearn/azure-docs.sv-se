---
title: Aktiviteten för gruppverifiering i Azure Data Factory | Microsoft Docs
description: Aktiviteten för Gruppverifiering fortsätter inte körning av pipelinen förrän den validerar anslutna datauppsättningen med vissa kriterier som användaren anger.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764330"
---
# <a name="validation-activity-in-azure-data-factory"></a>Aktiviteten för gruppverifiering i Azure Data Factory
Du kan använda en verifiering i en pipeline för att säkerställa att pipelinen endast fortsätter körningen när den har verifierat den bifogade datamängdsreferens finns, att den uppfyller de angivna villkoren eller timeout har uppnåtts.


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


## <a name="type-properties"></a>Egenskaperna för anslutningstypen

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på 'Verifiera'-aktivitet | String | Ja |
typ | Måste vara inställt på **verifiering**. | String | Ja |
dataset | Aktiviteten ska blockera körning tills den här datauppsättningen referensen har verifierats finns och att den uppfyller de angivna villkoren eller timeout har uppnåtts. Datauppsättning som angetts ska ha stöd för egenskapen ”MinimumSize” eller ”ChildItems”. | Datamängdsreferens | Ja |
timeout | Anger tidsgränsen för aktivitetens körning. Om inget värde anges är standardvärdet sju dagar (”7.00:00:00”). Formatet är d.hh:mm:ss | String | Nej |
Strömsparläge | En fördröjning i sekunder mellan försöken för verifiering. Om inget värde anges är standardvärdet 10 sekunder. | Integer | Nej |
childItems | Kontrollerar om mappen har underordnade objekt. Kan anges till true: Kontrollera att mappen finns och att det finns objekt. Blockerar tills det finns minst ett objekt i mappen eller tidsgränsen har nåtts.-false: Kontrollera att mappen finns och att den är tom. Värde att block tills mappen är tom eller tills tidsgränsen uppnås. Om inget värde har angetts, blockeras aktiviteten tills mappen finns eller tidsgränsen har nåtts. | Boolean | Nej |
minimumSize | Minsta storlek på en fil i byte. Om inget värde anges är standardvärdet 0 byte | Integer | Nej |


## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
