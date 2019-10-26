---
title: Funktioner i Azure Monitor logg frågor | Microsoft Docs
description: Den här artikeln beskriver hur du använder funktioner för att anropa en fråga från en annan logg fråga i Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 8d8473b88327d3d17346a0351d0a9fc510152cd8
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894180"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Använda funktioner i Azure Monitor logg frågor

Om du vill använda en logg fråga med en annan fråga kan du spara den som en funktion. På så sätt kan du förenkla komplexa frågor genom att dela upp dem i delar och du kan återanvända gemensam kod med flera frågor.

## <a name="create-a-function"></a>Skapa en funktion

Skapa en funktion med Log Analytics i Azure Portal genom att klicka på **Spara** och sedan ange informationen i följande tabell.

| Inställning | Beskrivning |
|:---|:---|
| Namn           | Visnings namn för frågan i **query Explorer**. |
| Spara som        | Funktion |
| Funktions Ali Aset | Kort namn för att använda funktionen i andra frågor. Får inte innehålla blank steg och måste vara unikt. |
| Kategori       | En kategori för att organisera sparade frågor och funktioner i **query Explorer**. |

> [!NOTE]
> En funktion i Azure Monitor kan inte innehålla en annan funktion.




## <a name="use-a-function"></a>Använd en funktion
Använd en funktion genom att inkludera dess alias i en annan fråga. Den kan användas som vilken annan tabell som helst.

## <a name="example"></a>Exempel
Följande exempel fråga returnerar alla saknade säkerhets uppdateringar som rapporter ATS under den senaste dagen. Spara den här frågan som en funktion med aliaset _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Skapa en ny fråga och referera till funktionen _security_updates_last_day_ för att söka efter SQL-relaterade nödvändiga säkerhets uppdateringar.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att skriva Azure Monitor logg frågor:

- [Sträng åtgärder](string-operations.md)
- [Datum-och tids åtgärder](datetime-operations.md)
- [Agg regerings funktioner](aggregations.md)
- [Avancerade agg regeringar](advanced-aggregations.md)
- [JSON och data strukturer](json-data-structures.md)
- [Kopplingar](joins.md)
- [Hierarkidiagram](charts.md)
