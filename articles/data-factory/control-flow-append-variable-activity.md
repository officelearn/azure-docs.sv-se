---
title: Lägg till variabel aktiviteten i Azure Data Factory | Microsoft Docs
description: Lär dig hur du ställer in aktiviteten lägga till variabeln för att lägga till ett värde i en befintlig matrisvariabel som definierats i Data Factory-pipeline
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 03652ce20d82565d5714cdc43a01a9e7c3074f6a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48904382"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Lägg till variabel aktiviteten i Azure Data Factory

Använd aktiviteten lägga till variabeln för att lägga till ett värde i en befintlig matrisvariabel som definierats i Data Factory-pipeline.

## <a name="type-properties"></a>Egenskaperna för anslutningstypen

Egenskap  | Beskrivning | Krävs
-------- | ----------- | --------
namn | Namnet på aktiviteten i pipelinen | Ja
beskrivning | Text som beskriver vad aktiviteten används | nej
typ | Aktivitetstyp är AppendVariable | ja
värde | Sträng literal eller uttryck Objektvärde som används för att lägga till en specifik variabel | ja
Variabelnamn | Namnet på variabeln som kommer att ändras av aktiviteten, variabeln måste vara av typen 'Array' | ja

## <a name="next-steps"></a>Nästa steg
Läs mer om en relaterade kontrollflödesaktivitet som stöds av Data Factory: 

- [Ställ in variabeln aktivitet](control-flow-set-variable-activity.md)
