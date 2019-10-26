---
title: Skriva effektiva logg frågor i Azure Monitor | Microsoft Docs
description: Referenser till resurser för att lära dig hur du skriver frågor i Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: a5ee03f6c42f076549856161a6ebe0b1888fe4aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894134"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Skriva effektiva logg frågor i Azure Monitor
Den här artikeln innehåller rekommendationer för att skriva effektiva logg frågor i Azure Monitor. Med hjälp av dessa strategier kan du se till att dina frågor kommer att köras snabbt och med minimalt överlappande.

## <a name="scope-your-query"></a>Omfånget för frågan
Om du har en fråga om mer data än vad du faktiskt behöver kan det leda till en lång körnings fråga och ofta leda till för mycket data i resultatet för att effektivt analysera. I extrema fall kan frågan till och med få timeout och Miss lyckas.

### <a name="specify-your-data-source"></a>Ange din data Källa
Det första steget i att skriva en effektiv fråga begränsar dess omfattning till de data källor som krävs. Att ange en tabell föredras alltid för att köra en bred texts ökning, till exempel `search *`. Om du vill fråga en speciell tabell startar du frågan med tabell namnet enligt följande:

``` Kusto
requests | ...
```

Du kan använda [Sök](/azure/kusto/query/searchoperator) för att söka efter ett värde över flera kolumner i vissa tabeller med hjälp av en fråga som följande:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Använd [union](/azure/kusto/query/unionoperator) för att fråga flera tabeller som följande:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Ange ett tidsintervall
Du bör också begränsa din fråga till tidsintervallet för de data som du vill ha. Som standard innehåller din fråga data som samlas in under de senaste 24 timmarna. Du kan ändra alternativet i [tidsintervalls väljaren](get-started-portal.md#select-a-time-range) eller lägga till det direkt i frågan. Det är bäst att lägga till tids filtret omedelbart efter tabell namnet så att resten av frågan endast bearbetar data inom intervallet:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Hämta bara de senaste posterna

Om du bara vill returnera de senaste posterna använder du operatorn *Top* som i följande fråga som returnerar de senaste 10 posterna som loggats i tabellen *spårning* :

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrera poster
Om du bara vill granska loggar som matchar ett angivet villkor använder du operatorn *WHERE* som i följande fråga som bara returnerar poster där värdet _severityLevel_ är högre än 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Sträng jämförelser
När du [utvärderar strängar](/azure/kusto/query/datatypes-string-operators)bör du vanligt vis använda `has` i stället för `contains` när du söker efter fullständiga tokens. `has` är mer effektivt eftersom det inte behöver söka efter del strängar.

## <a name="returned-columns"></a>Returnerade kolumner

Använd [Project](/azure/kusto/query/projectoperator) för att begränsa uppsättningen av kolumner som bearbetas till enbart de som du behöver:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Även om du kan använda [utöka](/azure/kusto/query/extendoperator) för att beräkna värden och skapa egna kolumner, är det oftast mer effektivt att filtrera på en tabell kolumn.

Exempel: den första frågan nedan som filtrerar på _åtgärd\_namn_ är mer effektiv än den andra som skapar en ny _prenumerations_ kolumn och filter på den:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Använda kopplingar
När du använder [kopplings](/azure/kusto/query/joinoperator) operatorn väljer du tabellen med färre rader som ska finnas på vänster sida av frågan.


## <a name="next-steps"></a>Nästa steg
Mer information om metod tips för frågor finns i [metod tips för frågor](/azure/kusto/query/best-practices).