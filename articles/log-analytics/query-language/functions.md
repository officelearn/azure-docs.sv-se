---
title: Fungerar Azure Log Analytics | Microsoft Docs
description: Den här artikeln beskriver hur du använder functions för att anropa en fråga från en annan fråga i Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 23079c265cc0c7bd8aa11270ecd38003d87eb30f
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855163"
---
# <a name="using-functions-in-azure-monitor-log-analytics"></a>Med hjälp av funktioner i Azure Monitor Log Analytics

> [!NOTE]
> Bör du genomföra [Kom igång med Analytics-portalen](get-started-analytics-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Om du vill använda en Log Analytics-fråga med en annan fråga kan du spara den som en funktion. Det gör att du kan förenkla komplexa frågor genom att dela upp dem i delar och att du kan återanvända gemensam kod med flera frågor.

## <a name="create-a-function"></a>Skapa en funktion

Skapa en funktion i Azure portal genom att klicka på **spara** och sedan tillhandahålla informationen i följande tabell.

| Inställning | Beskrivning |
|:---|:---|
| Namn           | Namn för frågan i **frågeutforskaren**. |
| Spara som        | Funktion |
| Funktionsalias | Kort namn att använda funktionen i andra frågor. Får inte innehålla blanksteg och måste vara unika. |
| Kategori       | En kategori för att organisera sparade frågor och funktioner i **frågeutforskaren**. |

> [!NOTE]
> En funktion i Log Analytics får inte innehålla en annan funktion.

> [!NOTE]
> Det är möjligt i Log Analytics-frågor, men för närvarande inte för Application Insights frågor att spara en funktion.



## <a name="use-a-function"></a>Använda en funktion
Funktionen genom att inkludera dess alias i en annan fråga. Det kan användas som andra tabeller.

## <a name="example"></a>Exempel
Följande exempelfråga returnerar alla saknade säkerhetsuppdateringar som rapporteras i den sista dagen. Spara den här frågan som en funktion med alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Skapa en till att söka efter SQL-relaterade nödvändiga säkerhetsuppdateringar.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda Log Analytics-frågespråket:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)