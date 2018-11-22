---
title: Analysera Log Analytics-data i Azure Monitor | Microsoft Docs
description: Du behöver en loggsökning för att hämta alla data från Log Analytics.  Den här artikeln beskriver hur nya log sökningar som används i Log Analytics och innehåller begrepp som du måste förstå innan du skapar en.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 921be3f6cc793857a701cb56236d75680d0c1232
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282319"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>Analysera Log Analytics-data i Azure Monitor

Loggdata som samlas in av Azure Monitor lagras i en Log Analytics-arbetsyta som är baserad på [Azure Data Explorer](/azure/data-explorer). Den samlar in telemetri från olika källor och använder den [frågespråket från Datautforskaren](/azure/kusto/query) att hämta och analysera data.

> [!NOTE]
> Log Analytics har tidigare behandlas som en egen tjänst i Azure. Det är nu vara en del av Azure Monitor och fokuserar på lagring och analys av loggdata med dess frågespråk. Funktioner som har som anses del av Log Analytics, till exempel Windows och Linux-agenter för datainsamling, vyer för att visualisera befintliga data och aviseringar att informera dig om problem, har inte ändrats men anses nu del av Azure Monitor.



## <a name="log-queries"></a>Loggfrågor

Du behöver en loggfråga för att hämta alla data från Log Analytics.  Om du är [analysera data i portalen](log-analytics-log-search-portals.md), [konfigurera en varningsregel](../monitoring-and-diagnostics/alert-metric.md) för att aviseras om ett visst villkor eller hämta data med hjälp av den [Log Analytics API](https://dev.loganalytics.io/), du använder en fråga för att ange de data du vill.  Den här artikeln beskriver hur du använder loggfrågor i Log Analytics och innehåller begrepp som du bör känna till innan du skapar en.



## <a name="where-log-queries-are-used"></a>Där loggfrågor används

Olika sätt som du ska använda frågor i Log Analytics är följande:

- **Portaler.** Du kan utföra interaktiva analyser av loggdata i de [Azure-portalen](log-analytics-log-search-portals.md).  På så sätt kan du redigera din fråga och analysera resultaten i olika format och visualiseringar.  
- **Varningsregler.** [Aviseringsregler](../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktivt identifiera problem från data i din arbetsyta.  Varje varningsregel baseras på en loggsökning som körs automatiskt med jämna mellanrum.  Resultatet är föremål för att avgöra om en avisering ska skapas.
- **Instrumentpaneler.** Du kan fästa resultatet av en fråga till en [Azure-instrumentpanelen](log-analytics-dashboards.md) som gör att du kan visualisera loggen och mätdata tillsammans och du kan också dela med andra Azure-användare. 
- **Vyer.**  Du kan skapa visualiseringar av data som ska ingå i instrumentpaneler för användare med [Vydesigner](log-analytics-view-designer.md).  Loggfrågor tillhandahåller de data som används av [paneler](log-analytics-view-designer-tiles.md) och [visualisering delar](log-analytics-view-designer-parts.md) i varje vy.  
- **Exportera.**  När du importerar data från Log Analytics-arbetsyta till Excel eller [Power BI](log-analytics-powerbi.md), skapar du en loggfråga för att definiera data som ska exporteras.
- **PowerShell.** Du kan köra ett PowerShell-skript från en kommandorad eller ett Azure Automation-runbook som använder [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) att hämta data från Log Analytics.  Denna cmdlet kräver en fråga för att fastställa data som ska hämtas.
- **Log Analytics-API.**  Den [Log Analytics Logga API: et search](../monitoring-and-diagnostics/monitoring-overview-alerts.md) tillåter alla REST API-klient att hämta loggdata från arbetsytan.  API-begäran innehåller en fråga som körs mot Log Analytics för att fastställa data som ska hämtas.

![Loggsökningar](media/log-analytics-queries/queries-overview.png)

## <a name="write-a-query"></a>Skriv en fråga
Logga Analytics använder [en version av Data Explorer frågespråk](query-language/get-started-queries.md) att hämta och analysera loggdata i en mängd olika sätt.  Du börjar vanligtvis med grundläggande frågor och sedan vidare om du vill använda mer avancerade funktionerna när behoven blir mer komplexa.

Den grundläggande strukturen i en fråga är en källtabellen följt av en serie operatörer avgränsade med ett vertikaltecken `|`.  Du kan länka samman flera operatorer för att förfina data och utföra avancerade funktioner.

Anta exempelvis att du vill hitta de översta tio datorerna med de flesta felhändelser under den senaste dagen.

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

Eller kanske du vill söka efter datorer som inte har haft ett pulsslag under den senaste dagen.

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

Vad gäller detta ett linjediagram med processoranvändning för varje dator från förra veckan?

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

Du ser i exemplen snabb att oavsett vilken typ av data som du arbetar med strukturen för frågan är liknande.  Du kan dela upp det i steg där den resulterande data från ett kommando skickas via pipelinen med nästa kommando.

Du kan också fråga efter data i Log Analytics-arbetsytor i din prenumeration.

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-log-analytics-data-is-organized"></a>Så här Log Analytics-data ordnas
När du skapar en fråga kan börja du med att bestämma vilka tabeller innehåller de data som du letar efter. Olika typer av data är indelade i dedikerade tabeller i varje [Log Analytics-arbetsyta](log-analytics-quick-create-workspace.md).  Dokumentationen för olika datakällor som innehåller namnet på datatypen som skapas och en beskrivning av var och en av dess egenskaper.  Många frågor kräver endast data från en enskild tabell, men andra kan använda en mängd olika alternativ för att inkludera data från flera tabeller.

Medan [Application Insights](../application-insights/app-insights-overview.md) butiker programdata som begäranden, undantag, spårningar och användning i Log Analytics, dessa data lagras i en annan partition än andra loggdata. Du använder samma frågespråk för att komma åt dessa data men måste använda den [Application Insights-konsolen](../application-insights/app-insights-analytics.md) eller [Application Insights REST API](https://dev.applicationinsights.io/) att komma åt den. Du kan använda [frågor över flera resurser](log-analytics-cross-workspace-search.md) att kombinera Application Insights-data med andra data i Log Analytics.


![Tabeller](media/log-analytics-queries/queries-tables.png)







## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [portaler som du använder för att skapa och redigera loggsökningar](log-analytics-log-search-portals.md).
- Kolla in en [självstudiekurs om hur du skriver frågor](log-analytics-tutorial-viewdata.md) med hjälp av det nya frågespråket.
