---
title: Prestandaräknare i Application Insights | Microsoft Docs
description: Övervaka systemet och anpassad .NET-prestandaräknare i Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: 3131afb31fd08903bb349f86634d2b9e6449c59e
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752664"
---
# <a name="system-performance-counters-in-application-insights"></a>Systemprestandaräknare i Application Insights

Windows innehåller en mängd olika [prestandaräknare](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) , till exempel processor användandet, minne, disk och nätverksanvändning. Du kan även definiera dina egna prestandaräknare. Så länge ditt program körs under IIS på en lokal värd eller virtuell dator som du har administrativ åtkomst.

## <a name="view-counters"></a>Visa räknare

Rutan mått visar standarduppsättningen med prestandaräknare.

![Prestandaräknare som rapporteras i Application Insights](./media/app-insights-performance-counters/performance-counters.png)

De aktuella standard räknare som samlas in för .NET-webbprogram är:

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

Om du vill se alla dina mest användbara diagram på samma ställe, skapa en [instrumentpanelen](../azure-monitor/app/app-insights-dashboards.md) och fästa dem till den.

## <a name="add-counters"></a>Lägg till räknare

Om prestandaräknare som du vill att inte ingår i listan över mått, kan du lägga till den.

1. Ta reda på vilka räknare som är tillgängliga i servern med hjälp av det här PowerShell-kommandot på den lokala servern:
   
    `Get-Counter -ListSet *`
   
    (Se [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Öppna ApplicationInsights.config.
   
   * Lägg till Application Insights i din app under utvecklingen och redigera ApplicationInsights.config i projektet och distribuera om den till dina servrar.
   * Om du har använt statusövervakaren kan instrumentera en webbapp under körning, hitta ApplicationInsights.config i rotkatalogen för appen i IIS. Uppdatera den det i varje server-instans.
3. Redigera direktivet prestanda insamlaren:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Du kan avbilda standard räknare och du har implementerat själv. `\Objects\Processes` är ett exempel på en standard räknare som är tillgänglig på alla Windows-System. `\Sales(photo)\# Items Sold` är ett exempel på en anpassad räknare som kan implementeras på en webbtjänst. 

Formatet är `\Category(instance)\Counter"`, eller kategorier som inte har instanser, bara `\Category\Counter`.

`ReportAs` krävs för namn på prestandaräknare som inte matchar `[a-zA-Z()/-_ \.]+` – det vill säga de innehåller tecken som inte finns i följande: bokstäver, avrunda hakparenteser, snedstreck, bindestreck, understreck, blanksteg, punkt.

Om du anger en instans, den kommer att samlas in som en dimension ”CounterInstanceName” för den rapporterade måtten.

### <a name="collecting-performance-counters-in-code"></a>Insamling av prestandaräknare i kod
Om du vill samla in prestandaräknare för system och skicka dem till Application Insights, kan du anpassa kodavsnittet nedan:


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
Du kan söka efter och visa räknare prestandarapporter i [Analytics](../azure-monitor/app/analytics.md).

Den **performanceCounters** schemat exponerar den `category`, `counter` namn, och `instance` för varje prestandaräknare.  I telemetri för varje program visas endast räknarna för programmet. Till exempel om du vill se vilka räknare är tillgängliga: 

![Prestandaräknare i Application Insights analytics](./media/app-insights-performance-counters/analytics-performance-counters.png)

('Instance' här refererar till prestandaräknarinstans, inte rollen eller serverinstansen för datorn. Instans prestandaräknarnamnet vanligtvis segment räknare, till exempel processortid av namnet på process eller program.)

Hämta ett diagram av det tillgängliga minnet under den senaste perioden: 

![Minne tidsdiagram i Application Insights analytics](./media/app-insights-performance-counters/analytics-available-memory.png)

Som andra telemetri **performanceCounters** också har en kolumn `cloud_RoleInstance` värde som anger identiteten för värdserverinstansens som din app körs. Till exempel att jämföra prestanda för din app på olika datorer: 

![Prestanda uppdelat efter rollinstans i Application Insights analytics](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET och Application Insights-antal
*Vad är skillnaden mellan det antal undantag och undantag mått?*

* *Antal undantag* är en system-prestandaräknare. CLR räknar alla hanterade och ohanterade undantag som utlöses och delar det totala antalet i ett exempelintervall med längden på intervallet. Application Insights SDK samlar in det här resultatet och skickar det till portalen.

* *Undantag* antal TrackException rapporterna tas emot av portalen i insamlingsintervall för diagrammet. Den innehåller endast hanterade undantag där du har skrivit TrackException anropar i din kod och inte innehåller alla [ohanterade undantag](../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Prestandaräknare i ASP.Net Core-program
Prestandaräknare stöds bara om programmet är inställd på fullständiga .NET Framework. Det finns ingen möjlighet att samla in prestandaräknare för .net Core program.

## <a name="alerts"></a>Aviseringar
Till exempel andra mått, kan du [ställa in en avisering](../azure-monitor/app/alerts.md) att varna dig om en prestandaräknare går utanför en gräns som du anger. Öppna fönstret aviseringar och klicka på Lägg till avisering.

## <a name="next"></a>Nästa steg
* [Beroendespårning](../azure-monitor/app/asp-net-dependencies.md)
* [Undantagsspårning](../azure-monitor/app/asp-net-exceptions.md)

