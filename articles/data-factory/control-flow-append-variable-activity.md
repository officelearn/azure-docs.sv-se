---
title: Lägga till variabel aktivitet i Azure Data Factory
description: Lär dig hur du ställer in aktiviteten Lägg till variabel för att lägga till ett värde i en befintlig matrisvariabel som definierats i en Data Factory-pipeline
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5d74cd0fcd524f00d79eb3fbab386c602a413766
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414199"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Lägga till variabel aktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Använd aktiviteten Lägg till variabel för att lägga till ett värde i en befintlig matrisvariabel som definierats i en Data Factory-pipeline.

## <a name="type-properties"></a>Egenskaper för typ

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
namn | Namnet på den på gång som den verksamhet som är i gång | Ja
description | Text som beskriver vad aktiviteten gör | nej
typ | Aktivitetstypen är Lägg tillVariabel | ja
värde | Stränglitteralt eller uttrycksobjektvärde som används för att lägga till i angiven variabel | ja
variableName (variabelNamn) | Namnet på variabeln som ska ändras efter aktivitet, variabeln måste vara av typen Array | ja

## <a name="next-steps"></a>Nästa steg
Lär dig mer om en relaterad kontrollflödesaktivitet som stöds av Data Factory: 

- [Ange variabelaktivitet](control-flow-set-variable-activity.md)
