---
title: Ange variabel aktivitet i Azure Data Factory
description: Lär dig hur du använder aktiviteten ange variabel för att ange värdet för en befintlig variabel som definierats i en Data Factory pipeline
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 2589e56d3170b235f31d07f902eb75c749e8573f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679306"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ange variabel aktivitet i Azure Data Factory

Använd aktiviteten ange variabel för att ange värdet för en befintlig variabel av typen sträng, bool eller matris som definierats i en Data Factory pipeline.

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
namn | Namn på aktiviteten i pipeline | Ja
description | Text som beskriver vad aktiviteten gör | nej
typ | Aktivitets typen är SetVariable | ja
värde | Sträng tecken eller uttrycks objekt värde som används för att ange angiven variabel | ja
variableName | Namnet på den variabel som ska anges av den här aktiviteten | ja


## <a name="next-steps"></a>Nästa steg
Lär dig mer om en relaterad kontroll flödes aktivitet som stöds av Data Factory: 

- [Lägg till variabel aktivitet](control-flow-append-variable-activity.md)
