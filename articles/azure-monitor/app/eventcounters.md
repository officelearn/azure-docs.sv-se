---
title: Händelseräknare i Application Insights | Microsoft-dokument
description: Övervaka system och anpassade .NET/.NET Core EventCounters i Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663597"
---
# <a name="eventcounters-introduction"></a>EventCounters introduktion

`EventCounter`är .NET/.NET Core mekanism för att publicera och konsumera räknare eller statistik. [Det här](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) dokumentet `EventCounters` innehåller en översikt över och exempel på hur du publicerar och använder dem. EventCounters stöds i alla OS-plattformar - Windows, Linux och macOS. Det kan ses som en plattformsoberoende motsvarighet för [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) som bara stöds i Windows-system.

Användare kan publicera `EventCounters` alla anpassade för att uppfylla sina behov, men körningen .NET Core 3.0 publicerar som standard en uppsättning av dessa räknare. Dokumentet går igenom de steg som `EventCounters` krävs för att samla in och visa (systemdefinierad eller användardefinierad) i Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Använda programinsikter för att samla in EventCounters

Application Insights `EventCounters` stöder `EventCounterCollectionModule`insamling med dess , som är en del av det nyutgivna nuget-paketet [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`aktiveras automatiskt när du använder [antingen AspNetCore](asp-net-core.md) eller [WorkerService](worker-service.md). `EventCounterCollectionModule`samlar räknare med en icke-konfigurerbar insamlingsfrekvens på 60 sekunder. Det finns inga särskilda behörigheter som krävs för att samla in EventCounters.

## <a name="default-counters-collected"></a>Standardräknare som samlats in

För appar som körs i .NET Core 3.0 samlas följande räknare in automatiskt av SDK. Namnet på räknarna kommer att vara av formuläret "Kategori| Counter".

|Kategori | Räknare|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Räknare i kategorin Microsoft.AspNetCore.Hosting läggs bara till i ASP.NET Core Applications.

## <a name="customizing-counters-to-be-collected"></a>Anpassa räknare som ska samlas in

I följande exempel visas hur du lägger till/tar bort räknare. Den här anpassningen `ConfigureServices` skulle göras i metoden för ditt program efter Application `AddApplicationInsightsTelemetry()` Insights telemetri samling är aktiverad med antingen eller `AddApplicationInsightsWorkerService()`. Följande är en exempelkod från ett ASP.NET Core-program. För andra typer av program, se [det här](worker-service.md#configuring-or-removing-default-telemetrymodules) dokumentet.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Händelseräknare i Måttutforskaren

Om du vill visa EventCounter-mått i [Metric Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)väljer du Application Insights-resurs och väljer Loggbaserade mått som måttnamnområde. Sedan visas EventCounter-mått under Anpassad kategori.

> [!div class="mx-imgBorder"]
> ![Händelseräknare som rapporterats i Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Händelseräknare i Analytics

Du kan också söka efter och visa händelseräknare i [Analytics](../../azure-monitor/app/analytics.md)i tabellen **customMetrics.**

Kör till exempel följande fråga för att se vilka räknare som samlas in och är tillgängliga för frågor:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Händelseräknare som rapporterats i Application Insights](./media/event-counters/analytics-event-counters.png)

Om du vill hämta ett diagram `ThreadPool Completed Work Item Count`över en viss räknare (till exempel: ) under den senaste perioden kör du följande fråga.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chatta med en enda räknare i Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Precis som annan telemetri har **customMetrics** också en kolumn `cloud_RoleInstance` som anger identiteten på värdserverinstansen där appen körs. Ovanstående fråga visar räknarvärdet per instans och kan användas för att jämföra prestanda för olika serverinstanser.

## <a name="alerts"></a>Aviseringar
Precis som andra mått kan du [ställa in en avisering](../../azure-monitor/app/alerts.md) för att varna dig om en händelseräknare går utanför en gräns som du anger. Öppna fönstret Aviseringar och klicka på Lägg till avisering.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Kan jag se EventCounters i Live Metrics?

Live Metrics visar inte EventCounters från och med idag. Använd Metric Explorer eller Analytics för att se telemetrin.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Vilka plattformar kan jag se standardlistan för .NET Core 3.0-räknare?

EventCounter kräver inga särskilda behörigheter och stöds på alla plattformar .NET Core 3.0 stöds. Det här omfattar:

* **Operativsystem**: Windows, Linux eller macOS.
* **Hosting metod**: I processen eller ur processen.
* **Distributionsmetod**: Ramberoende eller fristående.
* **Webbserver**: IIS (Internet Information Server) eller Kestrel.
* **Värdplattform:** Funktionen Webbappar i Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS) och så vidare.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Jag har aktiverat Application Insights från Azure Web App Portal. Men jag kan inte se EventCounters.?

 [Application Insights-tillägget](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) för ASP.NET Core stöder ännu inte den här funktionen. Det här dokumentet uppdateras när den här funktionen stöds.

## <a name="next-steps"></a><a name="next"></a>Nästa steg

* [Spårning av beroende](../../azure-monitor/app/asp-net-dependencies.md)
