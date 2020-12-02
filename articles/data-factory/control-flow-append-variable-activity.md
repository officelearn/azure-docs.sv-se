---
title: Lägg till variabel aktivitet i Azure Data Factory
description: Lär dig hur du ställer in aktiviteten Lägg till variabel för att lägga till ett värde till en befintlig mat ris variabel som definieras i en Data Factory pipeline
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 16bdd1d31440ed440faf67e939485da613e3886f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490949"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Lägg till variabel aktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Använd aktiviteten Lägg till variabel för att lägga till ett värde till en befintlig mat ris variabel som definierats i en Data Factory pipeline.

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
name | Namn på aktiviteten i pipeline | Yes
beskrivning | Text som beskriver vad aktiviteten gör | nej
typ | Aktivitets typen är AppendVariable | yes
värde | Sträng tecken eller uttrycks objekt värde som används för att lägga till angiven variabel | yes
variableName | Namnet på variabeln som ska ändras av aktivitet, variabeln måste vara av typen matris | ja

## <a name="next-steps"></a>Nästa steg
Lär dig mer om en relaterad kontroll flödes aktivitet som stöds av Data Factory: 

- [Ange variabelaktivitet](control-flow-set-variable-activity.md)
