---
title: Lägg till variabel aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du ställer in aktiviteten Lägg till variabel för att lägga till ett värde till en befintlig mat ris variabel som definieras i en Data Factory pipeline
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
ms.openlocfilehash: 24808c9ed4bda5e8d3b0ce23ab93bc59eb260374
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141730"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Lägg till variabel aktivitet i Azure Data Factory

Använd aktiviteten Lägg till variabel för att lägga till ett värde till en befintlig mat ris variabel som definierats i en Data Factory pipeline.

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Obligatorisk
-------- | ----------- | --------
name | Namn på aktiviteten i pipeline | Ja
description | Text som beskriver vad aktiviteten gör | nej
type | Aktivitets typen är AppendVariable | ja
value | Sträng tecken eller uttrycks objekt värde som används för att lägga till angiven variabel | ja
variableName | Namnet på variabeln som ska ändras av aktivitet, variabeln måste vara av typen matris | ja

## <a name="next-steps"></a>Nästa steg
Lär dig mer om en relaterad kontroll flödes aktivitet som stöds av Data Factory: 

- [Ange variabel aktivitet](control-flow-set-variable-activity.md)
