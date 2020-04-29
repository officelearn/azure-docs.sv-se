---
title: Funktioner i Azure Monitor logg frågor | Microsoft Docs
description: Den här artikeln beskriver hur du använder funktioner för att anropa en fråga från en annan logg fråga i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 7d94e53abbe8f4d2953729aa2363c3906ce94f74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670227"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Använda funktioner i Azure Monitor logg frågor

Om du vill använda en logg fråga med en annan fråga kan du spara den som en funktion. På så sätt kan du förenkla komplexa frågor genom att dela upp dem i delar och du kan återanvända gemensam kod med flera frågor.

## <a name="create-a-function"></a>Skapa en funktion

Skapa en funktion med Log Analytics i Azure Portal genom att klicka på **Spara** och sedan ange informationen i följande tabell.

| Inställningen | Beskrivning |
|:---|:---|
| Name           | Visnings namn för frågan i **query Explorer**. |
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

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)
