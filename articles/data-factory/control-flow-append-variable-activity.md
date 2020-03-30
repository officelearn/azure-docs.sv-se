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
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679980"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Lägga till variabel aktivitet i Azure Data Factory

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
