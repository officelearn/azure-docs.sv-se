---
title: Skriva effektiv loggfrågor i Azure Monitor | Microsoft Docs
description: Referenser till resurser för att lära dig hur du skriver frågor i Log Analytics.
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
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: 25d6b582ed4d4e24df3841f4191471296e25abd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519372"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Skriva effektiv loggfrågor i Azure Monitor
Den här artikeln innehåller rekommendationer för att skriva effektiv loggfrågor i Azure Monitor. Med dessa strategier kan du se till att dina frågor körs snabbt och med minimal Uppsnappat.

## <a name="scope-your-query"></a>Begränsa din fråga
Med din fråga bearbeta mer data än vad du verkligen behöver kan leda till en tidskrävande fråga och leder ofta för mycket data i dina resultat för att effektivt analysera. I extrema fall frågan även timeout och misslyckas.

### <a name="specify-your-data-source"></a>Ange din datakälla
Det första steget i att skriva en fråga för effektiv begränsar dess räckvidd till dess nödvändiga datakällor. Ange en tabell är alltid önskade jämfört med att köra ett brett textsökning som `search *`. Om du vill fråga en viss tabell, starta din fråga med namnet på tabellen enligt följande:

``` Kusto
requests | ...
```

Du kan använda [search](/azure/kusto/query/searchoperator) att söka efter ett värde i flera kolumner i specifika tabeller med hjälp av en fråga som liknar följande:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Använd [union](/azure/kusto/query/unionoperator) att fråga flera tabeller som liknar följande:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Ange ett tidsintervall
Du bör också begränsa frågan till tidsintervallet för data som du behöver. Som standard innehåller frågan data som samlas in under de senaste 24 timmarna. Du kan ändra alternativet i den [tid intervallet väljare](get-started-portal.md#select-a-time-range) eller uttryckligen lägga till den i din fråga. Det är bäst att lägga till tidsfiltret omedelbart efter tabellens namn så att resten av frågan endast bearbetar data inom detta område:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Hämta endast de senaste posterna

Du kan returnera endast de senaste posterna med den *upp* operator som i följande fråga som returnerar de 10 senaste posterna inloggad på *spårningar* tabell:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrera poster
Om du vill granska loggarna som matchar ett visst villkor, använda den *där* operator som i följande fråga som returnerar bara de poster där den _severityLevel_ värdet är högre än 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Strängjämförelser
När [utvärderar strängar](/azure/kusto/query/datatypes-string-operators), bör du vanligtvis använda `has` i stället för `contains` vid sökning efter fullständiga token. `has` är mer effektivt eftersom det inte har för att söka efter för delsträngar.

## <a name="returned-columns"></a>Returnerade kolumner

Använd [projekt](/azure/kusto/query/projectoperator) att begränsa uppsättningen kolumner som bearbetas till enbart de som du behöver:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Du kan använda [utöka](/azure/kusto/query/extendoperator) beräknar värden och skapa dina egna kolumner genom det vanligtvis bara mer effektivt att filtrera på en tabellkolumn.

Till exempel den första frågan nedan som filtrerar på _åtgärden\_namn_ skulle vara mer effektivt än det andra som skapar en ny _prenumeration_ kolumn och filter på den:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Med hjälp av kopplingar
När du använder den [join](/azure/kusto/query/joinoperator) -operator, Välj tabellen med färre rader som ska finnas på vänster sida av frågan.


## <a name="next-steps"></a>Nästa steg
Läs mer om bästa praxis för frågan i [fråga metodtips](/azure/kusto/query/best-practices).