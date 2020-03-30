---
title: Ange variabel aktivitet i Azure Data Factory
description: Lär dig hur du använder aktiviteten Ange variabel för att ange värdet för en befintlig variabel som definierats i en Data Factory-pipeline
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 88500ecbc56b34551a0cbd3ca94727ba4bbcda9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930647"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ange variabel aktivitet i Azure Data Factory

Använd aktiviteten Ange variabel för att ange värdet för en befintlig variabel av typen Sträng, Bool eller Matris som definierats i en Data Factory-pipeline.

## <a name="type-properties"></a>Egenskaper för typ

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
namn | Namnet på den på gång som den verksamhet som är i gång | Ja
description | Text som beskriver vad aktiviteten gör | nej
typ | Aktivitetstypen är SetVariable | ja
värde | Stränglitteralt eller uttrycksobjektvärde som används för att ange angiven variabel | ja
variableName (variabelNamn) | Namnet på den variabel som ska ställas in av den här aktiviteten | ja


## <a name="next-steps"></a>Nästa steg
Lär dig mer om en relaterad kontrollflödesaktivitet som stöds av Data Factory: 

- [Lägga till variabelaktivitet](control-flow-append-variable-activity.md)
