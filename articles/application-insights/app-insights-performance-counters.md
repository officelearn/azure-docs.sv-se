---
title: "Prestandaräknare i Application Insights | Microsoft Docs"
description: "Övervaka system och anpassade .NET-prestandaräknare i Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: mbullwin
ms.openlocfilehash: 26837a72dd4539cd5b32e5b49a127a714f3a1426
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="system-performance-counters-in-application-insights"></a>Prestandaräknare för system i Application Insights
Windows tillhandahåller flera olika [prestandaräknare](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters) , till exempel CPU användandet, minne, disk och användningen av nätverket. Du kan också definiera egna. [Application Insights](app-insights-overview.md) kan du visa de här prestandaräknarna om programmet körs under IIS på en lokal värd eller virtuell dator som du har administratörsbehörighet. Diagrammen övriga resurser som är tillgängliga för ditt liveprogram och hjälper dig för att identifiera Obalanserat belastningen mellan server-instanser.

Prestandaräknare visas i bladet servrar som innehåller en tabell som segment av server-instans.

![Prestandaräknare som rapporteras i Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

(Prestandaräknare är inte tillgängliga för Azure Web Apps. Men du kan [skickar Azure-diagnostik till Application Insights](app-insights-azure-diagnostics.md).)

## <a name="view-counters"></a>Visa räknare
Bladet servrar innehåller en standarduppsättning prestandaräknare. 

Om du vill visa andra räknare redigera diagram på bladet servrar, eller öppna ett nytt [Metrics Explorer](app-insights-metrics-explorer.md) bladet och lägga till nya diagram. 

Tillgängliga räknare listas som mått när du redigerar ett diagram.

![Prestandaräknare som rapporteras i Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Om du vill se alla mest användbara diagram på ett ställe, skapa en [instrumentpanelen](app-insights-dashboards.md) och fästa dem till den.

## <a name="add-counters"></a>Lägga till räknare
Om prestandaräknare som du vill använda inte visas i listan över mått är eftersom Application Insights SDK inte samla in den på din webbserver. Du kan konfigurera den att göra det.

1. Ta reda på vilka räknare som är tillgängliga i din server med hjälp av PowerShell-kommando på servern:
   
    `Get-Counter -ListSet *`
   
    (Se [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Öppna ApplicationInsights.config.
   
   * Om du lagt till Application Insights appen under utvecklingen redigera ApplicationInsights.config i projektet och sedan omdistribuera den till dina servrar.
   * Om du använde Status Monitor kan instrumentera en webbapp vid körning kan hitta ApplicationInsights.config i rotkatalogen för appen i IIS. Uppdatera den där på varje server-instans.
3. Redigera direktivet prestanda insamlaren:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Du kan avbilda standard räknare och du har implementerat själv. `\Objects\Processes`är ett exempel på en standard räknare tillgänglig på alla Windows-datorer. `\Sales(photo)\# Items Sold`är ett exempel på en anpassad räknare som kan implementeras i en webbtjänst. 

Formatet är `\Category(instance)\Counter"`, eller kategorier som inte har instanser, bara `\Category\Counter`.

`ReportAs`krävs för räknarnamn som inte matchar `[a-zA-Z()/-_ \.]+` – det vill säga de innehåller tecken som inte ingår i följande: bokstäver avrunda hakparenteser, snedstreck, bindestreck, understreck, blanksteg, punkt.

Om du anger en instans, samlas den som en dimension ”CounterInstanceName” av rapporterade mått.

### <a name="collecting-performance-counters-in-code"></a>Insamling av prestandaräknare i koden
Om du vill samla in prestandaräknare för system och skicka dem till Application Insights, kan du anpassa kodfragmentet nedan:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Eller så kan du göra samma sak med anpassade mått som du skapade:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Prestandaräknare i Analytics
Du kan söka efter och visa rapporter för räknaren av prestanda i [Analytics](app-insights-analytics.md).

Den **performanceCounters** schemat visar den `category`, `counter` namn, och `instance` namn för varje prestandaräknare.  Räknarna för programmet visas i telemetri för varje program. Till exempel om du vill se vilka räknare är tillgängliga: 

![Prestandaräknare i Application Insights analytics](./media/app-insights-performance-counters/analytics-performance-counters.png)

(Här 'Instansen' refererar till prestandaräknarinstans, inte den roll eller datorn serverinstansen. Instansen prestandaräknarnamnet vanligtvis segment räknare, till exempel processortid av namnet på processen eller programmet.)

Hämta ett diagram av minne under de senaste perioden: 

![Minne timechart i Application Insights analytics](./media/app-insights-performance-counters/analytics-available-memory.png)

Som andra telemetri **performanceCounters** också har en kolumn `cloud_RoleInstance` som anger att identiteten hos värden serverinstansen som appen körs. Till exempel för att jämföra prestandan för din app på olika datorer: 

![Prestanda åtskilda med rollinstans i Application Insights analytics](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET och Application Insights-antal
*Vad är skillnaden mellan undantag hastighet och undantag mått?*

* *Undantag hastighet* är en system-prestandaräknare. CLR räknar alla hanterade och ohanterade undantag som utlöses och delar av totalen i ett exempelintervall med längden på intervallet. Application Insights SDK samlar in det här resultatet och skickar det till portalen.
* *Undantag* antal TrackException-rapporter som tagits emot av portalen insamlingsintervall i diagrammet. Den innehåller bara de hanterade undantag där du har skrivit TrackException anropar i koden, och inte innehåller alla [ohanterade undantag](app-insights-asp-net-exceptions.md). 

## <a name="alerts"></a>Aviseringar
Precis som andra mått kan du [anger att en varning](app-insights-alerts.md) som varnar om en prestandaräknare går utanför en gräns som du anger. Öppna bladet aviseringar och klicka på Lägg till avisering.

## <a name="next"></a>Nästa steg
* [Beroende spårning](app-insights-asp-net-dependencies.md)
* [Undantagsspårning](app-insights-asp-net-exceptions.md)

