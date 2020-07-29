---
title: Kopplingar i Azure Monitor logg frågor | Microsoft Docs
description: Den här artikeln innehåller en lektion om hur du använder kopplingar i Azure Monitor logg frågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77670210"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Kopplingar i Azure Monitor logg frågor

> [!NOTE]
> Du bör slutföra [Kom igång med Azure Monitor Log Analytics](get-started-portal.md) och [Azure Monitor logg frågor](get-started-queries.md) innan du går igenom den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Med kopplingar kan du analysera data från flera tabeller, i samma fråga. De sammanfogar raderna med två data uppsättningar genom att matcha värdena i de angivna kolumnerna.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

I det här exemplet är det första data uppsättnings filtret för alla inloggnings händelser. Detta är anslutet med en andra data uppsättning som filtrerar för alla inloggnings händelser. De beräknade kolumnerna är _Computer_, _Account_, _TargetLogonId_och _TimeGenerated_. Data uppsättningarna korreleras av en delad kolumn, _TargetLogonId_. Utdata är en enskild post per korrelation, som har både inloggnings-och utloggnings tid.

Om båda data uppsättningarna har kolumner med samma namn, skulle kolumnerna i data uppsättningen på den högra sidan få ett index nummer, så i det här exemplet skulle resultaten Visa _TargetLogonId_ med värden från den vänstra tabellen och _TargetLogonId1_ med värden från den högra tabellen. I det här fallet togs den andra _TargetLogonId1_ -kolumnen bort med hjälp av `project-away` operatorn.

> [!NOTE]
> För att förbättra prestandan ska du bara behålla relevanta kolumner i de anslutna data uppsättningarna med `project` operatorn.


Använd följande syntax för att ansluta två data uppsättningar och den kopplade nyckeln har ett annat namn mellan de två tabellerna:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Uppslags tabeller
En vanlig användning av kopplingar använder statiska mappningar av värden med hjälp av `datatable` som kan hjälpa till med att omvandla resultatet till ett mer beskrivande sätt. Till exempel för att utöka säkerhets händelse data med händelse namnet för varje händelse-ID.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Delta med en DataTable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Kopplings typer
Ange typ av anslutning med _typ_ argumentet. Varje typ utför en annan matchning mellan posterna i de tabeller som anges i följande tabell.

| Kopplingstyp | Beskrivning |
|:---|:---|
| innerunique | Detta är standard läget för koppling. De första värdena i den matchade kolumnen i den vänstra tabellen hittas och dubblettvärden tas bort.  Sedan matchas uppsättningen av unika värden mot den högra tabellen. |
| innersta | Endast matchande poster i båda tabellerna ingår i resultaten. |
| leftouter | Alla poster i den vänstra tabellen och matchande poster i den högra tabellen ingår i resultaten. Omatchade utmatnings egenskaper innehåller null-värden.  |
| leftanti | Poster från den vänstra sidan som inte har några matchningar från höger ingår i resultaten. Resultat tabellen har bara kolumner från den vänstra tabellen. |
| leftsemi | Poster från den vänstra sidan som har matchningar från höger ingår i resultaten. Resultat tabellen har bara kolumner från den vänstra tabellen. |


## <a name="best-practices"></a>Bästa praxis

Tänk på följande när du ska uppnå optimala prestanda:

- Använd ett tids filter i varje tabell för att minska de poster som måste utvärderas för kopplingen.
- Använd `where` och `project` för att minska antalet rader och kolumner i inmatade tabeller före kopplingen.
- Om en tabell alltid är mindre än den andra, använder du den som vänster i kopplingen.


## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda Azure Monitor logg frågor:

- [Strängåtgärder](string-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad frågeskrivning](advanced-query-writing.md)
- [Diagram](charts.md)
