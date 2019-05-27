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
ms.openlocfilehash: cff4aaaab97fdcecab9cdf1d0dff2786f86b604b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966710"
---
# <a name="system-performance-counters-in-application-insights"></a>Systemprestandaräknare i Application Insights

Windows har en mängd olika [prestandaräknare](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) för exempelvis användning av processorer, minne, diskar och nätverk. Du kan även definiera dina egna prestandaräknare. Insamling av prestandaräknare stöds så länge ditt program körs under IIS på en lokal värd eller virtuell dator som du har administrativ åtkomst. Om program som körs som Azure Web Apps inte har direkt åtkomst till prestandaräknare, en delmängd av tillgängliga räknare samlas in av Application Insights.

## <a name="view-counters"></a>Visa räknare

Rutan mått visar standarduppsättningen med prestandaräknare.

![Prestandaräknare som rapporteras i Application Insights](./media/performance-counters/performance-counters.png)

De aktuella standard räknare som är konfigurerade som ska samlas in för ASP.NET/ASP.NET Core-webbprogram är:

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

Om du vill se alla dina mest användbara diagram på samma ställe, skapa en [instrumentpanelen](../../azure-monitor/app/app-insights-dashboards.md) och fästa dem till den.

## <a name="add-counters"></a>Lägg till räknare

Om prestandaräknare som du vill att inte ingår i listan över mått, kan du lägga till den.

1. Ta reda på vilka räknare som är tillgängliga i servern med hjälp av det här PowerShell-kommandot på den lokala servern:

    `Get-Counter -ListSet *`

    (Se [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Öppna ApplicationInsights.config.

   * Lägg till Application Insights i din app under utvecklingen och redigera ApplicationInsights.config i projektet och distribuera om den till dina servrar.
3. Redigera direktivet prestanda insamlaren:

```XML

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

> [!NOTE]
> ASP.NET Core-program inte har `ApplicationInsights.config`, och därför metoden ovan är inte giltigt för ASP.NET Core-program.

Du kan avbilda standard räknare och du har implementerat själv. `\Objects\Processes` är ett exempel på en standard räknare som är tillgänglig på alla Windows-System. `\Sales(photo)\# Items Sold` är ett exempel på en anpassad räknare som kan implementeras på en webbtjänst.

Formatet är `\Category(instance)\Counter"`, eller kategorier som inte har instanser, bara `\Category\Counter`.

`ReportAs` krävs för namn på prestandaräknare som inte matchar `[a-zA-Z()/-_ \.]+` – det vill säga de innehåller tecken som inte finns i följande: bokstäver, avrunda hakparenteser, snedstreck, bindestreck, understreck, blanksteg, punkt.

Om du anger en instans, den kommer att samlas in som en dimension ”CounterInstanceName” för den rapporterade måtten.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Insamling av prestandaräknare i koden för ASP.NET-webbprogram och.NET/.NET Core-konsolprogram
Om du vill samla in prestandaräknare för system och skicka dem till Application Insights, kan du anpassa kodavsnittet nedan:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Eller så kan du göra samma sak med anpassade mått som du skapade:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Insamling av prestandaräknare i koden för ASP.NET Core-webbprogram

Ändra `ConfigureServices` -metod i din `Startup.cs` klassen enligt nedan.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Prestandaräknare i Analytics
Du kan söka efter och visa räknare prestandarapporter i [Analytics](../../azure-monitor/app/analytics.md).

Den **performanceCounters** schemat exponerar den `category`, `counter` namn, och `instance` för varje prestandaräknare.  I telemetri för varje program visas endast räknarna för programmet. Till exempel om du vill se vilka räknare är tillgängliga: 

![Prestandaräknare i Application Insights analytics](./media/performance-counters/analytics-performance-counters.png)

('Instance' här refererar till prestandaräknarinstans, inte rollen eller serverinstansen för datorn. Instans prestandaräknarnamnet vanligtvis segment räknare, till exempel processortid av namnet på process eller program.)

Hämta ett diagram av det tillgängliga minnet under den senaste perioden: 

![Minne tidsdiagram i Application Insights analytics](./media/performance-counters/analytics-available-memory.png)

Som andra telemetri **performanceCounters** också har en kolumn `cloud_RoleInstance` värde som anger identiteten för värdserverinstansens som din app körs. Till exempel att jämföra prestanda för din app på olika datorer: 

![Prestanda uppdelat efter rollinstans i Application Insights analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET och Application Insights-antal

*Vad är skillnaden mellan det antal undantag och undantag mått?*

* *Antal undantag* är en system-prestandaräknare. CLR räknar alla hanterade och ohanterade undantag som utlöses och delar det totala antalet i ett exempelintervall med längden på intervallet. Application Insights SDK samlar in det här resultatet och skickar det till portalen.

* *Undantag* antal TrackException rapporterna tas emot av portalen i insamlingsintervall för diagrammet. Den innehåller endast hanterade undantag där du har skrivit TrackException anropar i din kod och inte innehåller alla [ohanterade undantag](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Prestandaräknare för program som körs i Azure Web Apps

Både ASP.NET och ASP.NET Core-program som distribueras till Azure Web Apps körs i en särskild sandbox-miljö. Den här miljön tillåter inte direkt åtkomst till systemprestandaräknare. Men en begränsad delmängd av prestandaräknare visas som miljövariabler enligt [här](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK för ASP.NET och ASP.NET Core samlar in prestandaräknare från Azure Web Apps från dessa särskilda miljövariabler. Endast en delmängd av räknare är tillgängliga i den här miljön och en fullständig lista finns [här.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Prestandaräknare i ASP.NET Core-program

* [ASP.NET Core SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.4.1 och samlar in prestandaräknare ovan om programmet körs i Azure Web App (Windows)

* SDK-version 2.7.0-beta3 och samlar in prestandaräknare ovan om programmet körs i Windows och riktar in sig på `NETSTANDARD2.0` eller högre.
* För program som riktar in sig på .NET Framework, stöds prestandaräknare i alla versioner av SDK.
* Den här artikeln kommer att uppdateras när du lägger till stöd för räknaren av prestanda i icke-Windows.

## <a name="alerts"></a>Aviseringar
Till exempel andra mått, kan du [ställa in en avisering](../../azure-monitor/app/alerts.md) att varna dig om en prestandaräknare går utanför en gräns som du anger. Öppna fönstret aviseringar och klicka på Lägg till avisering.

## <a name="next"></a>Nästa steg

* [Beroendespårning](../../azure-monitor/app/asp-net-dependencies.md)
* [Undantagsspårning](../../azure-monitor/app/asp-net-exceptions.md)

