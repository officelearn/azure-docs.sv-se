---
title: Händelse räknare i Application Insights | Microsoft Docs
description: Övervaka system och anpassade .NET/.NET Core-EventCounters i Application Insights.
services: application-insights
documentationcenter: ''
author: cithomas
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: cithomas
ms.openlocfilehash: fc9148d4f4c5920210b9218ca70f270bae3b663b
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273930"
---
# <a name="eventcounters-introduction"></a>Introduktion till EventCounters

`EventCounter`är .NET/.NET Core-mekanismen för att publicera och använda räknare eller statistik. [Det här](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) dokumentet ger en översikt `EventCounters` över och exempel på hur du publicerar och använder dem. EventCounters stöds i alla OS-plattformar – Windows, Linux och macOS. Det kan ses som en likvärdig plattform för [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) som endast stöds i Windows-system.

Användare kan publicera alla anpassade `EventCounters` efter deras behov, men .net Core 3,0-körningen publicerar som standard en uppsättning dessa räknare. Dokumentet går igenom de steg som krävs för att samla in och `EventCounters` Visa (systemdefinierad eller användardefinierad) i Azure Application insikter.

## <a name="using-application-insights-to-collect-eventcounters"></a>Använda Application Insights för att samla in EventCounters

Application Insights stöder insamling `EventCounters` med sitt `EventCounterCollectionModule`, som är en del av det nyligen utgivna NuGet-paketet [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`aktive ras automatiskt när du använder antingen [AspNetCore](asp-net-core.md) eller [WorkerService](worker-service.md). `EventCounterCollectionModule`samlar in räknare med en icke konfigurerbar samlings frekvens på 60 sekunder. Det finns inga särskilda behörigheter som krävs för att samla in EventCounters.

## <a name="default-counters-collected"></a>Insamlade standard räknare

För appar som körs i .NET Core 3,0 samlas följande räknare in automatiskt av SDK: n. Namnet på räknarna kommer att ha formatet "kategori | Counter ".

|Category | Räknare|
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
> Räknare för kategorin Microsoft. AspNetCore. hosting läggs bara till i Asp.Net Core-program.

## <a name="customizing-counters-to-be-collected"></a>Anpassa räknare som ska samlas in

I följande exempel visas hur du lägger till/tar bort räknare. Den här anpassningen görs i `ConfigureServices` -metoden för ditt program när Application Insights telemetri-samling har Aktiver ATS `AddApplicationInsightsWorkerService()` `AddApplicationInsightsTelemetry()` med hjälp av eller. Följande är en exempel kod från ett ASP.NET Core-program. Information om andra typer av program finns i [det här](worker-service.md#configuring-or-removing-default-telemetrymodules) dokumentet.

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

## <a name="event-counters-in-metric-explorer"></a>Händelse räknare i Metric Explorer

Om du vill visa EventCounter-mått i [Metric Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)väljer du Application Insights resurs och väljer loggbaserade mått som mått namn område. Sedan visas EventCounter mått under PerformanceCounter kategori.

> [!div class="mx-imgBorder"]
> ![Händelse räknare som rapporter ATS i Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Händelse räknare i Analytics

Du kan också söka efter och Visa rapporter för händelse räknare i [Analytics](../../azure-monitor/app/analytics.md)i **performanceCounters** -tabellen.

Kör till exempel följande fråga för att se vilka räknare som samlas in och är tillgängliga för fråga:

```Kusto
performanceCounters | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Händelse räknare som rapporter ATS i Application Insights](./media/event-counters/analytics-event-counters.png)

Kör följande fråga för att hämta ett diagram över en viss räknare `ThreadPool Completed Work Item Count`(till exempel:) under den senaste perioden.

```Kusto
performanceCounters 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chatta med en enda räknare i Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Precis som andra telemetri innehåller **performanceCounters** också en kolumn `cloud_RoleInstance` som anger identiteten för den värd Server instans där appen körs. Frågan ovan visar räknar värdet per instans och kan användas för att jämföra prestanda för olika Server instanser.

## <a name="alerts"></a>Aviseringar
Precis som med andra mått kan du [Ange en avisering](../../azure-monitor/app/alerts.md) som varnar dig om en händelse räknare går utanför en gräns som du anger. Öppna fönstret aviseringar och klicka på Lägg till avisering.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Kan jag se EventCounters i Live-mått?

Live mått visar inte EventCounters från och med idag. Använd Metric Explorer eller Analytics för att se Telemetrin.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Vilka plattformar kan jag se standard listan med .NET Core 3,0-räknare?

EventCounter kräver inte några särskilda behörigheter och stöds i alla plattformar .NET Core 3,0 stöds. Det här omfattar:

* **Operativsystem**: Windows, Linux eller macOS.
* **Värd metod**: I processen eller utanför processen.
* **Distributions metod**: Ramverks beroende eller fristående.
* **Webb server**: IIS (Internet Information Server) eller Kestrel.
* **Värd plattform**: Web Apps funktionen i Azure App Service, Azure VM, Docker, Azure Kubernetes service (AKS) och så vidare.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Jag har aktiverat Application Insights från Azure Web App-portalen. Men jag kan inte se EventCounters.?

 [Application Insights-tillägget](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) för ASP.net Core har ännu inte stöd för den här funktionen. Det här dokumentet kommer att uppdateras när den här funktionen stöds.

## <a name="next"></a>Nästa steg

* [Beroende spårning](../../azure-monitor/app/asp-net-dependencies.md)