---
title: Översikt över loggfrågor i Azure Monitor | Microsoft-dokument
description: Besvarar vanliga frågor relaterade till loggfrågor och får dig att börja använda dem.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670125"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Översikt över loggfrågor i Azure Monitor
Loggfrågor hjälper dig att fullt ut utnyttja värdet på de data som samlas in i [Azure Monitor Logs](../platform/data-platform-logs.md). Med ett kraftfullt frågespråk kan du koppla data från flera tabeller, aggregera stora datauppsättningar och utföra komplexa åtgärder med minimal kod. Praktiskt taget alla frågor kan besvaras och analys utföras så länge som stödjande data har samlats in, och du förstår hur du konstruerar rätt fråga.

Vissa funktioner i Azure Monitor, till exempel insikter och [lösningar,](../insights/solutions-inventory.md) [bearbetar](../insights/insights-overview.md) loggdata utan att utsätta dig för de underliggande frågorna. Om du till fullo vill utnyttja andra funktioner i Azure Monitor bör du förstå hur frågor skapas och hur du kan använda dem för att interaktivt analysera data i Azure Monitor Logs.

Använd den här artikeln som utgångspunkt för att lära dig mer om loggfrågor i Azure Monitor. Den besvarar vanliga frågor och innehåller länkar till annan dokumentation som ger ytterligare information och lektioner.

## <a name="how-can-i-learn-how-to-write-queries"></a>Hur kan jag lära mig att skriva frågor?
Om du vill hoppa rakt in i saker, kan du börja med följande tutorials:

- [Kom igång med Logganalys i Azure Monitor](get-started-portal.md).
- [Kom igång med loggfrågor i Azure Monitor](get-started-queries.md).

När du har grunderna nere, gå igenom flera lektioner med antingen dina egna data eller data från vår demomiljö som börjar med: 

- [Arbeta med strängar i Azure Monitor-loggfrågor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Vilket språk använder loggfrågor?
Azure Monitor Logs baseras på [Azure Data Explorer](/azure/data-explorer)och loggfrågor skrivs med samma Kusto-frågespråk (KQL). Detta är ett rikt språk som utformats för att vara lätt att läsa och författare, och du bör kunna börja använda den med minimal vägledning.

Se [Azure Data Explorer KQL-dokumentation](/azure/kusto/query) för fullständig dokumentation om KQL och referens på olika funktioner som är tillgängliga.<br>
Se [Komma igång med loggfrågor i Azure Monitor](get-started-queries.md) för en snabb genomgång av språket med hjälp av data från Azure Monitor Logs.
Se [Azure Monitor-loggfrågorsspråksskillnader](data-explorer-difference.md) för mindre skillnader i den version av KQL som används av Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Vilka data är tillgängliga för loggfrågor?
Alla data som samlas in i Azure Monitor Logs är tillgängliga för att hämta och analysera i loggfrågor. Olika datakällor skriver sina data till olika tabeller, men du kan inkludera flera tabeller i en enda fråga för att analysera data över flera källor. När du skapar en fråga börjar du med att bestämma vilka tabeller som har de data som du letar efter, så du bör ha åtminstone en grundläggande förståelse för hur data i Azure Monitor Logs är strukturerad.

Se [Källor för Azure Monitor-loggar](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), för en lista över olika datakällor som fyller i Azure Monitor Logs.<br>
Se [Strukturera Azure Monitor-loggar](logs-structure.md) för en förklaring av hur data är strukturerat.

## <a name="what-does-a-log-query-look-like"></a>Hur ser en loggfråga ut?
En fråga kan vara så enkel som ett enda tabellnamn för att hämta alla poster från den tabellen:

```Kusto
Syslog
```

Eller så kan det filtrera efter vissa poster, sammanfatta dem och visualisera resultaten i ett diagram:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

För mer komplex analys kan du hämta data från flera tabeller med hjälp av en koppling för att analysera resultaten tillsammans.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Även om du inte är bekant med KQL, bör du kunna åtminstone räkna ut den grundläggande logik som används av dessa frågor. De börjar med namnet på en tabell och lägger sedan till flera kommandon för att filtrera och bearbeta dessa data. En fråga kan använda valfritt antal kommandon och du kan skriva mer komplexa frågor när du bekantar dig med de olika KQL-kommandon som är tillgängliga.

Se [Komma igång med loggfrågor i Azure Monitor](get-started-queries.md) för en självstudiekurs om loggfrågor som introducerar språket och vanliga funktioner, .<br>


## <a name="what-is-log-analytics"></a>Vad är Log Analytics?
Log Analytics är det primära verktyget i Azure-portalen för att skriva loggfrågor och interaktivt analysera deras resultat. Även om en loggfråga används någon annanstans i Azure Monitor skriver du vanligtvis och testar frågan först med Log Analytics.

Du kan starta Log Analytics från flera platser i Azure-portalen. Omfattningen av de data som är tillgängliga för Log Analytics bestäms av hur du startar den. Mer information [finns i Frågeomfattning.](scope.md)

- Välj **Loggar** på **Azure Monitor-menyn** eller **menyn Logganalysarbetsytor.**
- Välj **Analys** på **översiktssidan för** ett program för programinsikt.
- Välj **Loggar** på menyn för en Azure-resurs.

![Log Analytics](media/log-query-overview/log-analytics.png)

Se [Kom igång med Logganalys i Azure Monitor](get-started-portal.md) för en självstudiegenomgång i Log Analytics som introducerar flera av dess funktioner.

## <a name="where-else-are-log-queries-used"></a>Var annars används loggfrågor?
Förutom att interaktivt arbeta med loggfrågor och deras resultat i Log Analytics, områden i Azure Monitor där du kommer att använda frågor inkluderar följande:

- **Varningsregler.** [Varningsregler](../platform/alerts-overview.md) identifierar proaktivt problem från data på arbetsytan.  Varje varningsregel baseras på en loggsökning som körs automatiskt med jämna mellanrum.  Resultaten granskas för att avgöra om en avisering ska skapas.
- **Instrumentpaneler.** Du kan fästa resultaten av en fråga i en [Azure-instrumentpanel](../learn/tutorial-logs-dashboards.md) som gör att du kan visualisera logg- och måttdata tillsammans och eventuellt dela med andra Azure-användare.
- **Visningar.**  Du kan skapa visualiseringar av data som ska ingå i användarinstrumentpaneler med [View Designer](../platform/view-designer.md).  Loggfrågor tillhandahåller de data som används av [paneler](../platform/view-designer-tiles.md) och [visualiseringsdelar](../platform/view-designer-parts.md) i varje vy.  
- **Exportera.**  När du importerar loggdata från Azure Monitor till Excel eller [Power BI](../platform/powerbi.md)skapar du en loggfråga för att definiera de data som ska exporteras.
- **Powershell.** Du kan köra ett PowerShell-skript från en kommandorad eller en Azure Automation-runbook som använder [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) för att hämta loggdata från Azure Monitor.  Den här cmdlet kräver en fråga för att bestämma vilka data som ska hämtas.
- **API för Azure Monitor loggar.**  [Azure Monitor Logs API](https://dev.loganalytics.io) gör det möjligt för alla REST API-klienter att hämta loggdata från arbetsytan.  API-begäran innehåller en fråga som körs mot Azure Monitor för att fastställa vilka data som ska hämtas.


## <a name="next-steps"></a>Nästa steg
- Gå igenom en [självstudiekurs om hur du använder Log Analytics i Azure-portalen](get-started-portal.md).
- Gå igenom en [handledning om att skriva frågor](get-started-queries.md).
