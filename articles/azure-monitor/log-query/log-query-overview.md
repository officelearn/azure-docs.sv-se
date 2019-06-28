---
title: Översikt över log frågor i Azure Monitor | Microsoft Docs
description: Svar frågor som rör frågor och hjälper dig att komma igång med att använda dem.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: 7605bf36c41c5b1276d29076173efd52409afaa9
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310343"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Översikt över loggfrågor i Azure Monitor
Loggfrågor hjälper dig att utnyttjar fullt ut värdet för data som samlas in i [Azure Monitor-loggar](../platform/data-platform-logs.md). Ett kraftfullt frågespråk kan du ansluta till data från flera tabeller, aggregerar stora mängder data och utföra avancerade åtgärder med minimal kod. I stort sett alla frågor besvaras och analys utföras så länge stödjande data har samlats in och hur du ska skapa rätt frågan.

Vissa funktioner i Azure Monitor som [insights](../insights/insights-overview.md) och [lösningar](../insights/solutions-inventory.md) bearbetar loggdata utan att exponera du till de underliggande frågorna. Om du vill utnyttjar fullt ut andra funktioner i Azure Monitor, bör du förstå hur frågor skapas och hur du kan använda dem för att interaktivt analysera data i Azure Monitor-loggar.

Använd den här artikeln som utgångspunkt för att lära dig mer om loggfrågor i Azure Monitor. Den ger svar på vanliga frågor och innehåller länkar till annan dokumentation som innehåller ytterligare information och lektioner.

## <a name="how-can-i-learn-how-to-write-queries"></a>Hur kan jag lära mig hur du skriver frågor?
Om du vill sätta till saker kan du börja med följande självstudiekurser:

- [Kom igång med Log Analytics i Azure Monitor](get-started-portal.md).
- [Kom igång med loggfrågor i Azure Monitor](get-started-queries.md).

När du har grunderna ned kan du gå igenom flera lektioner med dina egna data eller data från våra demomiljö som börjar med: 

- [Arbeta med strängar i Azure Monitor log-frågor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Vilket språk loggar frågor användning?
Azure Monitor-loggar är baserad på [Azure Data Explorer](/azure/data-explorer), och loggfrågor skrivs med samma Kusto-fråga språk (KQL). Detta är ett omfattande språk som utformats för att vara enkelt att läsa och författare och du bör kunna börja använda det med minimal vägledning.

Se [dokumentation om Azure Data Explorer KQL](/azure/kusto/query) fullständig dokumentation för KQL och referenser på olika funktioner som är tillgängliga.<br>
Se [Kom igång med loggfrågor i Azure Monitor](get-started-queries.md) en snabb genomgång av de språk med hjälp av data från Azure Monitor-loggar.
Se [Azure Monitor log-fråga språkskillnader](data-explorer-difference.md) för mindre skillnader i versionen av KQL som används av Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Vilka data som är tillgängliga för att logga frågor?
Alla data som samlas in i Azure Monitor-loggar finns att hämta och analysera i loggfrågor. Olika datakällor skriver sina data till olika tabeller, men du kan inkludera flera tabeller i en enskild fråga att analysera data från flera källor. När du skapar en fråga kan börja du med att bestämma vilka tabeller innehåller de data som du letar efter, så du bör ha minst en grundläggande förståelse för hur data i Azure Monitor-loggar är strukturerad.

Se [källor för Azure Monitor-loggar](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), för en lista över olika datakällor som fylla i Azure Monitor-loggar.<br>
Se [strukturen för Azure Monitor-loggar](logs-structure.md) för en förklaring av hur data är strukturerad.

## <a name="what-does-a-log-query-look-like"></a>Hur ser en loggfråga ut?
En fråga kan vara enkla som namnet på en tabell för att hämta alla poster från tabellen:

```Kusto
Syslog
```

Eller så kan filtrera efter specifika poster, summera dem och visualisera resultat i ett diagram:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

För mer komplexa analyser kan du hämta data från flera tabeller som använder en koppling för att analysera resultaten tillsammans.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Även om du inte är bekant med KQL, bör du kunna minst ta reda på den grundläggande logik som används av de här frågorna. De startar med namnet på en tabell och sedan lägga till flera kommandon för att filtrera och bearbetar dessa data. En fråga kan använda valfritt antal kommandon och du kan skriva mer komplexa frågor som du bekanta dig med de olika KQL kommandona som är tillgängliga.

Se [Kom igång med loggfrågor i Azure Monitor](get-started-queries.md) en självstudiekurs om loggfrågor som beskriver de språk och vanliga funktioner.<br>


## <a name="what-is-log-analytics"></a>Vad är Log Analytics?
Log Analytics är det primära verktyget i Azure-portalen för att skriva loggfrågor och analysera resultaten interaktivt. Även om en loggfråga används någon annanstans i Azure Monitor ska du normalt skriva och testa frågan först använda Log Analytics.

Du kan börja Log Analytics från olika platser i Azure-portalen. Omfånget för data som är tillgängliga för Log Analytics bestäms av hur du startar den. Se [Genomsökningsomfånget](scope.md) för mer information.

- Välj **loggar** från den **Azure Monitor** menyn eller **Log Analytics-arbetsytor** menyn.
- Välj **Analytics** från den **översikt** sidan av ett program med Application Insights.
- Välj **loggar** menyn till en Azure-resurs.

![Log Analytics](media/log-query-overview/log-analytics.png)

Se [Kom igång med Log Analytics i Azure Monitor](get-started-portal.md) stegvisa självstudier Log Analytics som introducerar flera av dess funktioner.

## <a name="where-else-are-log-queries-used"></a>Var används loggfrågor?
Utöver att arbeta interaktivt med loggfrågor och resultaten i Log Analytics, är områden i Azure Monitor där du ska använda frågor följande:

- **Varningsregler.** [Aviseringsregler](../platform/alerts-overview.md) proaktivt identifiera problem från data i din arbetsyta.  Varje varningsregel baseras på en loggsökning som körs automatiskt med jämna mellanrum.  Resultatet är föremål för att avgöra om en avisering ska skapas.
- **Instrumentpaneler.** Du kan fästa resultatet av en fråga till en [Azure-instrumentpanelen](../learn/tutorial-logs-dashboards.md) som gör att du kan visualisera loggen och mätdata tillsammans och du kan också dela med andra Azure-användare.
- **Vyer.**  Du kan skapa visualiseringar av data som ska ingå i instrumentpaneler för användare med [Vydesigner](../platform/view-designer.md).  Loggfrågor tillhandahåller de data som används av [paneler](../platform/view-designer-tiles.md) och [visualisering delar](../platform/view-designer-parts.md) i varje vy.  
- **Exportera.**  När du importerar loggdata från Azure Monitor till Excel eller [Power BI](../platform/powerbi.md), skapar du en loggfråga för att definiera data som ska exporteras.
- **PowerShell.** Du kan köra ett PowerShell-skript från en kommandorad eller ett Azure Automation-runbook som använder [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) att hämta loggdata från Azure Monitor.  Denna cmdlet kräver en fråga för att fastställa data som ska hämtas.
- **API för Azure Monitor-loggar.**  Den [API: T för Azure Monitor-loggar](../platform/alerts-overview.md) tillåter alla REST API-klient att hämta loggdata från arbetsytan.  API-begäran innehåller en fråga som körs mot Azure Monitor för att fastställa data som ska hämtas.


## <a name="next-steps"></a>Nästa steg
- Gå igenom en [självstudiekurs om hur du använder Log Analytics i Azure-portalen](get-started-portal.md).
- Gå igenom en [självstudiekurs om hur du skriver frågor](get-started-queries.md).
