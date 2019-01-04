---
title: Ställ in variabeln aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder aktiviteten ange variabel för att ange värdet för en befintlig variabel som definierats i Data Factory-pipeline
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: cc573028779bcd6b77394bbeefbea58f714b835c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017352"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ställ in variabeln aktiviteten i Azure Data Factory

Använd aktiviteten ange variabel för att ange värdet för en befintlig variabel av typen String, Bool eller matris som definierats i Data Factory-pipeline.

## <a name="type-properties"></a>Egenskaperna för anslutningstypen

Egenskap  | Beskrivning | Krävs
-------- | ----------- | --------
namn | Namnet på aktiviteten i pipelinen | Ja
beskrivning | Text som beskriver vad aktiviteten används | nej
typ | Aktivitetstyp är SetVariable | ja
värde | Sträng literal eller uttryck Objektvärde som används för att ange specifik variabel | ja
Variabelnamn | Namnet på den variabel som anges av den här aktiviteten | ja


## <a name="next-steps"></a>Nästa steg
Läs mer om en relaterade kontrollflödesaktivitet som stöds av Data Factory: 

- [Tilläggsaktivitet variabel](control-flow-append-variable-activity.md)
