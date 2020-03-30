---
title: Prestandaräknare i Application Insights | Microsoft-dokument
description: Övervaka system- och anpassade .NET-prestandaräknare i Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: 94d2520c17867f6d70caffd002a76365a425986f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669887"
---
# <a name="system-performance-counters-in-application-insights"></a>Systemprestandaräknare i Application Insights

Windows har en mängd olika [prestandaräknare](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) för exempelvis användning av processorer, minne, diskar och nätverk. Du kan också definiera dina egna prestandaräknare. Insamling av prestandaräknare stöds så länge ditt program körs under IIS på en lokal värd eller virtuell dator som du har administrativ åtkomst till. Även om program som körs som Azure Web Apps inte har direkt åtkomst till prestandaräknare, samlas en delmängd av tillgängliga räknare in av Application Insights.

## <a name="view-counters"></a>Visa räknare

Fönstret Mått visar standarduppsättningen med prestandaräknare.

![Prestandaräknare som rapporteras i Application Insights](./media/performance-counters/performance-counters.png)

De aktuella standardräknarna som är konfigurerade för att samlas in för ASP.NET/ASP.NET Core-webbprogram är:
- %\\processortid
- %\\processprocessprocessortid normaliserad
- Tillgängliga\\byte för minne
- ASP.NET begäranden/sek
- .NET CLR-undantag som har kastats / sek
- ASP.NET ProgramRequest körningstid
- Bearbeta\\privata byte
- Bearbeta\\IO-databyten per sekund
- ASP.NET programbegäranden\\i programkö
- Processor(_Total)\\% processortid

## <a name="add-counters"></a>Lägg till räknare

Om den prestandaräknare du vill ha inte finns med i listan över mått kan du lägga till den.

1. Ta reda på vilka räknare som är tillgängliga på servern med hjälp av det här PowerShell-kommandot på den lokala servern:

    `Get-Counter -ListSet *`

    (Se [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Öppna ApplicationInsights.config.

   * Om du har lagt till Application Insights i appen under utvecklingen redigerar du ApplicationInsights.config i projektet och distribuerar sedan om den till dina servrar.
3. Redigera direktivet om prestandainsamlare:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Core-program har `ApplicationInsights.config`inte , och därför är ovanstående metod inte giltig för ASP.NET Core Applications.

Du kan fånga både standardräknare och de du har implementerat själv. `\Objects\Processes`är ett exempel på en standardräknare som är tillgänglig på alla Windows-system. `\Sales(photo)\# Items Sold`är ett exempel på en anpassad räknare som kan implementeras i en webbtjänst.

Formatet är `\Category(instance)\Counter"`, eller för kategorier som inte `\Category\Counter`har instanser, bara .

`ReportAs`krävs för räknarnamn som `[a-zA-Z()/-_ \.]+` inte matchar - det vill än innehåller de tecken som inte finns i följande uppsättningar: bokstäver, runda parenteser, snedstreck framåt, bindestreck, understreck, blanksteg, punkt.

Om du anger en instans samlas den in som en dimension "CounterInstanceName" för det rapporterade måttet.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Samla in prestandaräknare i kod för ASP.NET webbprogram eller .NET/.NET Core Console Applications
Om du vill samla in systemprestandaräknare och skicka dem till Application Insights kan du anpassa kodavsnittet nedan:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Eller så kan du göra samma sak med anpassade mått som du har skapat:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Samla in prestandaräknare i kod för ASP.NET Core Web Applications

Ändra `ConfigureServices` metod `Startup.cs` i klassen enligt nedan.

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
Du kan söka efter och visa prestandaräknare i [Analytics](../../azure-monitor/app/analytics.md).

Schemat **performanceCounters** visar `category`namnet `counter` och `instance` namnet på varje prestandaräknare.  I telemetrin för varje program visas bara räknarna för det programmet. Om du till exempel vill se vilka räknare som är tillgängliga: 

![Prestandaräknare i Application Insights-analys](./media/performance-counters/analytics-performance-counters.png)

("Instans" här refererar till prestandaräknarinstansen, inte rollen eller servermaskinsinstansen. Namnet på prestandaräknareinstansen segmenterar vanligtvis räknare, till exempel processortid med namnet på processen eller programmet.)

Så här hämtar du ett diagram över tillgängligt minne under den senaste perioden: 

![Minnestidsdiagram i Application Insights-analys](./media/performance-counters/analytics-available-memory.png)

Precis som andra telemetri har **performanceCounters** också en kolumn `cloud_RoleInstance` som anger identiteten på värdserverinstansen där appen körs. Om du till exempel vill jämföra appens prestanda på de olika datorerna: 

![Prestanda segmenterad efter rollinstans i Application Insights-analys](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET och Application Insights räknas

*Vad är skillnaden mellan måtten Undantagsfrekvens och Undantag?*

* *Undantagsfrekvens* är en systemprestandaräknare. CLR räknar alla hanterade och ohanterade undantag som genereras och delar upp summan i ett samplingsintervall med intervallets längd. Application Insights SDK samlar in det här resultatet och skickar det till portalen.

* *Undantag* är ett antal TrackException-rapporter som tas emot av portalen i diagrammets samplingsintervall. Den innehåller endast hanterade undantag där du har skrivit TrackException-samtal i koden och inte innehåller alla [ohanterade undantag](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Prestandaräknare för program som körs i Azure Web Apps

Både ASP.NET och ASP.NET Core-program som distribueras till Azure Web Apps körs i en speciell sandlådemiljö. Den här miljön tillåter inte direkt åtkomst till systemprestandaräknare. En begränsad delmängd av räknare exponeras dock som miljövariabler enligt beskrivningen [här](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK för ASP.NET och ASP.NET Core samlar in prestandaräknare från Azure Web Apps från dessa speciella miljövariabler. Endast en delmängd av räknare är tillgängliga i den här miljön, och den fullständiga listan finns [här.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Prestandaräknare i ASP.NET Core-program

Stödet för prestandaräknare i ASP.NET Core är begränsat:

* [SDK-versioner](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.4.1 och senare samlar in prestandaräknare om programmet körs i Azure Web Apps (Windows).
* SDK-versioner 2.7.1 och senare samlar in prestandaräknare om `NETSTANDARD2.0` programmet körs i Windows och mål eller senare.
* För program som är inriktade på .NET Framework stöder alla versioner av SDK prestandaräknare.
* SDK Versioner 2.8.0 och senare stöder cpu / minne räknare i Linux. Ingen annan räknare stöds i Linux. Det rekommenderade sättet att få systemräknare i Linux (och andra icke-Windows-miljöer) är att använda [EventCounters](eventcounters.md)

## <a name="alerts"></a>Aviseringar
Precis som andra mått kan du [ställa in en avisering](../../azure-monitor/app/alerts.md) för att varna dig om en prestandaräknare går utanför en gräns som du anger. Öppna fönstret Aviseringar och klicka på Lägg till avisering.

## <a name="next-steps"></a><a name="next"></a>Nästa steg

* [Spårning av beroende](../../azure-monitor/app/asp-net-dependencies.md)
* [Spårning av undantag](../../azure-monitor/app/asp-net-exceptions.md)

