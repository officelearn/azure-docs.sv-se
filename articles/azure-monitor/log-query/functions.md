---
title: Funktioner i Azure Monitor-loggfrågor | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder funktioner för att anropa en fråga från en annan loggfråga i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 7d94e53abbe8f4d2953729aa2363c3906ce94f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670227"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Använda funktioner i Azure Monitor-loggfrågor

Om du vill använda en loggfråga med en annan fråga kan du spara den som en funktion. På så sätt kan du förenkla komplexa frågor genom att dela upp dem i delar och du kan återanvända vanlig kod med flera frågor.

## <a name="create-a-function"></a>Skapa en funktion

Skapa en funktion med Log Analytics i Azure-portalen genom att klicka på **Spara** och sedan ange informationen i följande tabell.

| Inställning | Beskrivning |
|:---|:---|
| Namn           | Visa namnet på frågan i **Frågeutforskaren**. |
| Spara som        | Funktion |
| Funktionsalias | Kortnamn för att använda funktionen i andra frågor. Får inte innehålla blanksteg och måste vara unika. |
| Kategori       | En kategori för att ordna sparade frågor och funktioner i **Frågeutforskaren**. |

> [!NOTE]
> En funktion i Azure Monitor kan inte innehålla en annan funktion.




## <a name="use-a-function"></a>Använda en funktion
Använd en funktion genom att inkludera dess alias i en annan fråga. Den kan användas som alla andra bord.

## <a name="example"></a>Exempel
Följande exempelfråga returnerar alla saknade säkerhetsuppdateringar som rapporterats under den senaste dagen. Spara den här frågan som en funktion med alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Skapa en annan fråga och referera _till funktionen security_updates_last_day_ för att söka efter SQL-relaterade nödvändiga säkerhetsuppdateringar.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att skriva Azure Monitor-loggfrågor:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Går](joins.md)
- [Diagram](charts.md)
