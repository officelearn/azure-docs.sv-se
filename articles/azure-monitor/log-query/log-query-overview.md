---
title: Analysera loggdata i Azure Monitor | Microsoft Docs
description: Du behöver en loggfråga för att hämta loggdata från Azure Monitor.  Den här artikeln beskriver hur nya log frågor används i Azure Monitor och innehåller begrepp som du måste förstå innan du skapar en.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: bwren
ms.openlocfilehash: 259a792acb4d2fb8f9695f0f5a856bdf5069f2cd
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749757"
---
# <a name="analyze-log-data-in-azure-monitor"></a>Analysera loggdata i Azure Monitor

Loggdata som samlas in av Azure Monitor lagras i en Log Analytics-arbetsyta som är baserad på [Azure Data Explorer](/azure/data-explorer). Den samlar in telemetri från olika källor och använder den [Kusto-frågespråket](/azure/kusto/query) används av Data Explorer för att hämta och analysera data.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="log-queries"></a>Loggfrågor

Du behöver en loggfråga för att hämta alla loggdata från Azure Monitor.  Om du är [analysera data i portalen](portals.md), [konfigurera en varningsregel](../platform/alerts-metric.md) för att aviseras om ett visst villkor eller hämta data med hjälp av den [API: T för Azure Monitor-loggar](https://dev.loganalytics.io/) , du använder en fråga för att ange de data du vill.  Den här artikeln beskriver hur du använder loggfrågor i Azure Monitor och innehåller begrepp som du bör känna till innan du skapar en.



## <a name="where-log-queries-are-used"></a>Där loggfrågor används

Olika sätt som du ska använda loggfrågor i Azure Monitor är följande:

- **Portalen.** Du kan utföra interaktiva analyser av loggdata i de [Azure-portalen](portals.md).  På så sätt kan du redigera din fråga och analysera resultaten i olika format och visualiseringar.  
- **Varningsregler.** [Aviseringsregler](../platform/alerts-overview.md) proaktivt identifiera problem från data i din arbetsyta.  Varje varningsregel baseras på en loggsökning som körs automatiskt med jämna mellanrum.  Resultatet är föremål för att avgöra om en avisering ska skapas.
- **Instrumentpaneler.** Du kan fästa resultatet av en fråga till en [Azure-instrumentpanelen](../learn/tutorial-logs-dashboards.md) som gör att du kan visualisera loggen och mätdata tillsammans och du kan också dela med andra Azure-användare. 
- **Vyer.**  Du kan skapa visualiseringar av data som ska ingå i instrumentpaneler för användare med [Vydesigner](../platform/view-designer.md).  Loggfrågor tillhandahåller de data som används av [paneler](../platform/view-designer-tiles.md) och [visualisering delar](../platform/view-designer-parts.md) i varje vy.  
- **Exportera.**  När du importerar loggdata från Azure Monitor till Excel eller [Power BI](../platform/powerbi.md), skapar du en loggfråga för att definiera data som ska exporteras.
- **PowerShell.** Du kan köra ett PowerShell-skript från en kommandorad eller ett Azure Automation-runbook som använder [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) att hämta loggdata från Azure Monitor.  Denna cmdlet kräver en fråga för att fastställa data som ska hämtas.
- **API för Azure Monitor-loggar.**  Den [API: T för Azure Monitor-loggar](../platform/alerts-overview.md) tillåter alla REST API-klient att hämta loggdata från arbetsytan.  API-begäran innehåller en fråga som körs mot Azure Monitor för att fastställa data som ska hämtas.

![Loggsökningar](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>Skriv en fråga
Azure Monitor använder [en version av det Kusto-frågespråket](get-started-queries.md) att hämta och analysera loggdata i en mängd olika sätt.  Du börjar vanligtvis med grundläggande frågor och sedan vidare om du vill använda mer avancerade funktionerna när behoven blir mer komplexa.

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

## <a name="how-azure-monitor-log-data-is-organized"></a>Hur Azure Monitor log-data ordnas
När du skapar en fråga kan börja du med att bestämma vilka tabeller innehåller de data som du letar efter. Olika typer av data är indelade i dedikerade tabeller i varje [Log Analytics-arbetsyta](../learn/quick-create-workspace.md).  Dokumentationen för olika datakällor som innehåller namnet på datatypen som skapas och en beskrivning av var och en av dess egenskaper.  Många frågor kräver endast data från en enskild tabell, men andra kan använda en mängd olika alternativ för att inkludera data från flera tabeller.

Medan [Application Insights](../app/app-insights-overview.md) butiker programdata som begäranden, undantag, spårningar, och användning i Azure Monitor-loggar, dessa data lagras i en annan partition än andra loggdata. Du använder samma frågespråk för att komma åt dessa data men måste använda den [Application Insights-konsolen](../app/analytics.md) eller [Application Insights REST API](https://dev.applicationinsights.io/) att komma åt den. Du kan använda [frågor över flera resurser](../log-query/cross-workspace-query.md) att kombinera Application Insights-data med andra loggdata i Azure Monitor.


![Tabeller](media/log-query-overview/queries-tables.png)




## <a name="next-steps"></a>Nästa steg
- Läs om hur du använder [loggsökningar i Log Analytics för att skapa och redigera](../log-query/portals.md).
- Kolla in en [självstudiekurs om hur du skriver frågor](../log-query/get-started-queries.md) med hjälp av det nya frågespråket.
