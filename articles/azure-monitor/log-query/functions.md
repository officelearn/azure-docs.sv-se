---
title: Funktioner i Azure Monitor logg frågor | Microsoft Docs
description: Den här artikeln beskriver hur du använder funktioner för att anropa en fråga från en annan logg fråga i Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 75beb7b66863efd2fb3679f034a3663dca4a6d2f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076703"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Använda funktioner i Azure Monitor logg frågor

Om du vill använda en logg fråga med en annan fråga kan du spara den som en funktion. På så sätt kan du förenkla komplexa frågor genom att dela upp dem i delar och du kan återanvända gemensam kod med flera frågor.

## <a name="create-a-function"></a>Skapa en funktion

Skapa en funktion med Log Analytics i Azure Portal genom att klicka på **Spara** och sedan ange informationen i följande tabell.

| Inställning | Beskrivning |
|:---|:---|
| Name           | Visnings namn för frågan i **query Explorer**. |
| Spara som        | Funktion |
| Funktionsalias | Kort namn för att använda funktionen i andra frågor. Får inte innehålla blank steg och måste vara unikt. |
| Category       | En kategori för att organisera sparade frågor och funktioner i **query Explorer**. |

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
