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
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: e904075908fe7108c0566856b25fe03be0b7fd86
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023826"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Lägg till variabel aktiviteten i Azure Data Factory

Använd aktiviteten lägga till variabeln för att lägga till ett värde i en befintlig matrisvariabel som definierats i Data Factory-pipeline.

## <a name="type-properties"></a>Egenskaperna för anslutningstypen

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
namn | Namnet på aktiviteten i pipelinen | Ja
beskrivning | Text som beskriver vad aktiviteten används | nej
typ | Aktivitetstyp är AppendVariable | ja
värde | Sträng literal eller uttryck Objektvärde som används för att lägga till en specifik variabel | ja
Variabelnamn | Namnet på variabeln som kommer att ändras av aktiviteten, variabeln måste vara av typen 'Array' | ja

## <a name="next-steps"></a>Nästa steg
Läs mer om en relaterade kontrollflödesaktivitet som stöds av Data Factory: 

- [Ställ in variabeln aktivitet](control-flow-set-variable-activity.md)
