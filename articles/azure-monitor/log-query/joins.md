---
title: Ansluter till Azure Monitor-loggfrågor | Microsoft-dokument
description: Den här artikeln innehåller en lektion om hur du använder kopplingar i Azure Monitor-loggfrågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670210"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Kopplingar till Azure Monitor-loggfrågor

> [!NOTE]
> Du bör slutföra [Komma igång med Azure Monitor Log Analytics](get-started-portal.md) och Azure [Monitor-loggfrågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Med kopplingar kan du analysera data från flera tabeller i samma fråga. Raderna med två datauppsättningar sammanfogas med matchande värden för angivna kolumner.


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

I det här exemplet filtrerar de första datauppsättningen för alla inloggningshändelser. Detta är förenat med en andra datauppsättning som filtrerar efter alla ut signeringshändelser. De projicerade kolumnerna är _Dator_, _Konto_, _TargetLogonId_och _TimeGenerated_. Datauppsättningarna är korrelerade med en delad kolumn, _TargetLogonId_. Utdata är en enda post per korrelation, som har både inloggnings- och ut logga uttid.

Om båda datauppsättningarna har kolumner med samma namn, skulle kolumnerna i högerdatauppsättningen få ett indexnummer, så i det här exemplet visar resultaten _TargetLogonId_ med värden från den vänstra tabellen och _TargetLogonId1_ med värden från den högra tabellen. I det här fallet togs den andra _TargetLogonId1-kolumnen_ bort med hjälp av operatorn. `project-away`

> [!NOTE]
> För att förbättra prestanda, behåll endast relevanta kolumner `project` i de kopplade datauppsättningarna med hjälp av operatorn.


Använd följande syntax för att sammanfoga två datauppsättningar och den kopplade nyckeln har ett annat namn mellan de två tabellerna:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Uppslagstabeller
En vanlig användning av kopplingar använder `datatable` statisk mappning av värden som använder som kan bidra till att omvandla resultaten till ett mer presentabelt sätt. Om du till exempel vill berika säkerhetshändelsedata med händelsenamnet för varje händelse-ID.

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

![Gå med i ett databord](media/joins/dim-table.png)

## <a name="join-kinds"></a>Gå med typer
Ange typ av koppling med _argumentet typ._ Varje typ utför en annan matchning mellan posterna i de angivna tabellerna enligt beskrivningen i följande tabell.

| Kopplingstyp | Beskrivning |
|:---|:---|
| innerunique | Detta är standardkopplingsläget. Först hittas värdena i den matchade kolumnen i den vänstra tabellen och dubblettvärden tas bort.  Därefter matchas uppsättningen unika värden mot rätt tabell. |
| Inre | Endast matchande poster i båda tabellerna ingår i resultatet. |
| leftouter | Alla poster i den vänstra tabellen och matchande poster i den högra tabellen ingår i resultatet. Omatchade utdataegenskaper innehåller nulls.  |
| leftanti (vänster- | Poster från vänster sida som inte har matchningar från höger ingår i resultatet. Resultattabellen har bara kolumner från den vänstra tabellen. |
| leftsemi (vänstersemi) | Poster från vänster sida som har matchningar från höger ingår i resultatet. Resultattabellen har bara kolumner från den vänstra tabellen. |


## <a name="best-practices"></a>Bästa praxis

Tänk på följande punkter för optimal prestanda:

- Använd ett tidsfilter i varje tabell för att minska de poster som måste utvärderas för kopplingen.
- Använd `where` `project` och för att minska antalet rader och kolumner i indatatabellerna före kopplingen.
- Om den ena tabellen alltid är mindre än den andra använder du den som vänster sida av kopplingen.


## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda Azure Monitor-loggfrågor:

- [Strängåtgärder](string-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad frågeskrivning](advanced-query-writing.md)
- [Diagram](charts.md)
