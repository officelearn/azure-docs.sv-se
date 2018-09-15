---
title: Kopplingar i Azure Log Analytics-frågor | Microsoft Docs
description: Den här artikeln innehåller en lektion med kopplingar i Log Analytics-frågespråket.
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
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: c24d79d6983f7c32f5c563192bcfe412da586ef2
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603496"
---
# <a name="joins-in-log-analytics-queries"></a>Kopplingar i Log Analytics-frågor

> [!NOTE]
> Bör du genomföra [Kom igång med Analytics-portalen](get-started-analytics-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Kopplingar kan du analysera data från flera tabeller i samma fråga. De sammanfogar raderna i två uppsättningar data efter matchande värdena i de angivna kolumnerna.


```KQL
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

I det här exemplet filtrerar den första datauppsättningen för alla inloggningshändelser. Detta är kopplad till en andra datauppsättning som filtrerar för alla utloggning händelser. De beräknade kolumnerna är _datorn_, _konto_, _TargetLogonId_, och _TimeGenerated_. Datauppsättningarna kopplas ihop av en delad kolumn _TargetLogonId_. Utdata är en post per korrelation, vilket visar båda inloggning och utloggning tid.

Om båda datauppsättningar har kolumner med samma namn, kolumner i datauppsättningen höger skulle ges ett indextal och i det här exemplet resultatet skulle alltså _TargetLogonId_ med värden från den vänstra tabellen och  _TargetLogonId1_ med värden från den högra tabellen. I det här fallet andra _TargetLogonId1_ kolumnen togs bort med hjälp av den `project-away` operator.

> [!NOTE]
> För att förbättra prestanda, Behåll endast relevanta kolumner i de anslutna-datauppsättningar, med hjälp av den `project` operator.


Använd följande syntax för att ansluta till två datauppsättningar och domänansluten nyckeln har ett annat namn mellan två tabeller:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Uppslagstabeller
Ett vanligt användningsområde för kopplingar använder statiska mappningen av värden med hjälp av `datatable` som kan underlätta vid övergång resultaten till blir lite snyggare. Till exempel om du vill utöka säkerheten händelsedata med händelsenamn för varje händelse-ID.

```KQL
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

![Ansluta med en datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Ansluta till typer
Ange vilken typ av koppling med den _typ_ argumentet. Varje typ av utför en annan matchning mellan poster i de angivna tabellerna som beskrivs i följande tabell.

| Kopplingstyp | Beskrivning |
|:---|:---|
| innerunique | Det här är standardläget för koppling. Först värdena för den matchande kolumnen i den vänstra tabellen finns och duplicerade värden tas bort.  Uppsättning med unika värden matchas sedan mot den högra tabellen. |
| inre | Endast matchande poster i båda tabellerna som ingår i resultatet. |
| leftouter | Alla poster i den vänstra tabellen och matchande poster i den högra tabellen ingår i resultatet. Oöverträffad output-egenskaper innehåller null-värden.  |
| leftanti | Poster från vänster sida som inte har några matchningar från höger ingår i resultatet. Endast kolumner från den vänstra tabellen har resultattabellen. |
| leftsemi | Poster från den vänstra sidan som har matchningar från höger ingår i resultatet. Endast kolumner från den vänstra tabellen har resultattabellen. |


## <a name="best-practices"></a>Bästa praxis

Tänk på följande för optimala prestanda:

- Använd ett tidsfilter i varje tabell för att minska de poster som ska utvärderas för kopplingen.
- Använd `where` och `project` att minska antalet rader och kolumner i tabellerna indata innan kopplingen.
* Om en tabell alltid är mindre än den andra, kan du använda den som vänster sida i kopplingen.


## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda Log Analytics-frågespråket:

- [Strängåtgärder](string-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Avancerad fråga skrivning](advanced-query-writing.md)
- [Diagram](charts.md)