---
title: Översikt över logg frågor i Azure Monitor | Microsoft Docs
description: Svarar på vanliga frågor om logg frågor och hjälper dig att komma igång med att använda dem.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 6cd2a28ddbe970385ff44deec2158c257937982e
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82591660"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Översikt över logg frågor i Azure Monitor
Med logg frågor kan du utnyttja värdet för de data som samlas in i [Azure Monitor loggar](../platform/data-platform-logs.md). Ett kraftfullt frågespråk gör att du kan koppla data från flera tabeller, aggregera stora mängder data och utföra komplexa åtgärder med minimal kod. I stort sett kan alla frågor besvaras och analyseras så länge som stödjande data har samlats in och du förstår hur du skapar rätt fråga.

Vissa funktioner i Azure Monitor, till exempel [insikter](../insights/insights-overview.md) och [lösningar](../insights/solutions-inventory.md) , bearbetar loggdata utan att du exponerar de underliggande frågorna. För att helt utnyttja andra funktioner i Azure Monitor bör du förstå hur frågor skapas och hur du kan använda dem för att interaktivt analysera data i Azure Monitor loggar.

Använd den här artikeln som en start punkt för att lära dig om logg frågor i Azure Monitor. Den svarar på vanliga frågor och innehåller länkar till annan dokumentation som innehåller mer information och lektioner.

## <a name="how-can-i-learn-how-to-write-queries"></a>Hur kan jag lära mig att skriva frågor?
Om du vill gå direkt till saker kan du börja med följande Självstudier:

- [Kom igång med Log Analytics i Azure Monitor](get-started-portal.md).
- [Kom igång med logg frågor i Azure Monitor](get-started-queries.md).

När du har fastställt grunderna kan du gå igenom flera lektioner med hjälp av antingen dina egna data eller data från vår demo miljö som börjar med: 

- [Arbeta med strängar i Azure Monitor logg frågor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Vilket språk använder logg frågor för att använda?
Azure Monitor loggar baseras på [Azure datautforskaren](/azure/data-explorer), och logg frågor skrivs med samma KQL (Kusto Query Language). Detta är ett omfattande språk som utformats för att vara lätt att läsa och redigera och du bör kunna börja använda det med minimal vägledning.

Mer information om KQL och referenser till olika funktioner finns i [dokumentationen för Azure DATAUTFORSKAREN KQL](/azure/kusto/query) .<br>
Se [Kom igång med logg frågor i Azure Monitor](get-started-queries.md) för en snabb genom gång av språket som använder data från Azure Monitor-loggar.
Se [Azure Monitor logg frågor språk skillnader](data-explorer-difference.md) för mindre skillnader i den version av KQL som används av Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Vilka data är tillgängliga för att logga frågor?
Alla data som samlas in i Azure Monitor-loggar är tillgängliga för att hämta och analysera i logg frågor. Olika data källor kommer att skriva sina data till olika tabeller, men du kan inkludera flera tabeller i en enda fråga för att analysera data över flera källor. När du skapar en fråga börjar du med att bestämma vilka tabeller som har de data som du letar efter, så du bör ha minst en grundläggande förståelse för hur data i Azure Monitor loggar är strukturerade.

En lista över olika data källor som fyller Azure Monitor loggar finns i [källor till Azure Monitor loggar](../platform/data-platform-logs.md#sources-of-azure-monitor-logs).<br>
Se [strukturen i Azure Monitor loggar](logs-structure.md) för en förklaring av hur data är strukturerade.

## <a name="what-does-a-log-query-look-like"></a>Vad ser en logg fråga ut?
En fråga kan vara lika enkel som ett enda tabell namn för att hämta alla poster från tabellen:

```Kusto
Syslog
```

Du kan också filtrera efter vissa poster, sammanfatta dem och visualisera resultatet i ett diagram:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

För mer komplex analys kan du hämta data från flera tabeller med en koppling för att analysera resultaten tillsammans.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Även om du inte är bekant med KQL bör du kunna ta bort den grundläggande logiken som används av dessa frågor minst. De börjar med namnet på en tabell och lägger sedan till flera kommandon för att filtrera och bearbeta dessa data. En fråga kan använda valfritt antal kommandon, och du kan skriva mer komplexa frågor när du blir bekant med de olika KQL-kommandona som är tillgängliga.

I [Kom igång med logg frågor i Azure Monitor](get-started-queries.md) finns en själv studie kurs om logg frågor som beskriver språk och vanliga funktioner.<br>


## <a name="what-is-log-analytics"></a>Vad är Log Analytics?
Log Analytics är det primära verktyget i Azure Portal för att skriva logg frågor och analysera resultaten interaktivt. Även om en loggfråga används någon annanstans i Azure Monitor skriver du vanligtvis och testar frågan först med Log Analytics.

Du kan starta Log Analytics från flera platser i Azure Portal. Omfattningen av de data som är tillgängliga för Log Analytics bestäms av hur du startar den. Mer information finns i [fråge omfånget](scope.md) .

- Välj **loggar** på menyn **Azure Monitor** eller **Log Analytics arbets ytor** .
- Välj **loggar** på sidan **översikt** i ett Application Insights-program.
- Välj **loggar** på menyn för en Azure-resurs.

![Log Analytics](media/log-query-overview/log-analytics.png)

Se [Kom igång med Log Analytics i Azure Monitor](get-started-portal.md) för en själv studie genom gång av Log Analytics som introducerar flera av dess funktioner.

## <a name="where-else-are-log-queries-used"></a>Var vill du använda logg frågor?
Förutom att interaktivt arbeta med logg frågor och deras resultat i Log Analytics, är områden i Azure Monitor där du kommer att använda frågorna följande:

- **Aviserings regler.** [Aviserings regler](../platform/alerts-overview.md) identifierar proaktivt problem från data i din arbets yta.  Varje varnings regel baseras på en loggs ökning som körs automatiskt med jämna mellanrum.  Resultaten kontrol leras för att avgöra om en avisering ska skapas.
- **Instrument paneler.** Du kan fästa resultatet av en fråga i en [Azure-instrumentpanel](../learn/tutorial-logs-dashboards.md) som gör att du kan visualisera logg-och mät data tillsammans och dela dem med andra Azure-användare.
- **Vyer.**  Du kan skapa visualiseringar av data som ska ingå i användar instrument paneler med [View Designer](../platform/view-designer.md).  Logg frågor ger data som används av [paneler](../platform/view-designer-tiles.md) och [visualiserings delar](../platform/view-designer-parts.md) i varje vy.  
- **Exporteras.**  När du importerar loggdata från Azure Monitor till Excel eller [Power BI](../platform/powerbi.md)skapar du en logg fråga som definierar de data som ska exporteras.
- **PowerShell.** Du kan köra ett PowerShell-skript från en kommando rad eller en Azure Automation Runbook som använder [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) för att hämta loggdata från Azure Monitor.  Denna cmdlet kräver en fråga för att avgöra vilka data som ska hämtas.
- **API för Azure Monitor loggar.**  Med [API: et för Azure Monitor loggar](https://dev.loganalytics.io) kan alla REST API klienter Hämta loggdata från arbets ytan.  API-förfrågan innehåller en fråga som körs mot Azure Monitor för att avgöra vilka data som ska hämtas.


## <a name="next-steps"></a>Nästa steg
- Gå igenom en [själv studie kurs om hur du använder Log Analytics i Azure Portal](get-started-portal.md).
- Gå igenom en [själv studie kurs om att skriva frågor](get-started-queries.md).
