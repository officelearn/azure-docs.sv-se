---
title: Aktivera Snapshot Debugger för .NET-appar i Azure Service Fabric, moln tjänster och Virtual Machines | Microsoft Docs
description: Aktivera Snapshot Debugger för .NET-appar i Azure Service Fabric, moln tjänster och Virtual Machines
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 5a6cf763ae16b55806df2acaf2e03fd8c13d1e76
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359288"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Aktivera Snapshot Debugger för .NET-appar i Azure Service Fabric, moln tjänster och Virtual Machines

Om ditt ASP.NET-eller ASP.NET Core-program körs i Azure App Service, rekommenderar vi starkt att du [aktiverar Snapshot debugger via Application Insights Portal sidan](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Men om ditt program kräver en anpassad Snapshot Debugger-konfiguration eller en för hands version av .NET Core, bör den här instruktionen följas ***utöver*** instruktionerna för att [Aktivera via Application Insights portalen Sidan](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Om ditt program körs i Azure Service Fabric, moln tjänst, Virtual Machines eller lokala datorer, bör du använda följande instruktioner. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurera insamling av ögonblicksbild för ASP.NET-program

1. [Aktivera Application Insights i din webbapp](../../azure-monitor/app/asp-net.md), om du inte gjort det ännu.

2. Inkludera den [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app.

3. Vid behov kan du anpassa Snapshot Debugger-konfigurationen som lagts till i [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Standard konfigurationen för Snapshot Debugger är oftast Tom och alla inställningar är valfria. Här är ett exempel som visar en konfiguration som motsvarar standard konfigurationen:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Ögonblicksbilder samlas endast på undantag som har rapporterats till Application Insights. I vissa fall (exempelvis äldre versioner av .NET-plattformen) kan du behöva [konfigurera insamling av undantag](../../azure-monitor/app/asp-net-exceptions.md#exceptions) vill visa undantag med ögonblicksbilder i portal.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Konfigurera ögonblicks bild samling för program som använder ASP.NET Core 2,0 eller senare

1. [Aktivera Application Insights i din ASP.NET Core-webbapp](../../azure-monitor/app/asp-net-core.md), om du inte gjort det ännu.

    > [!NOTE]
    > Att se till att ditt program refererar till version 2.1.1 eller senare av Microsoft.ApplicationInsights.AspNetCore-paketet.

2. Inkludera den [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app.

3. Ändra programmets `Startup` klassen för att lägga till och konfigurera den Ögonblicksbildsinsamlaren telemetri processor.
    1. Om [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paket version 1.3.5 eller senare används, lägger du till följande using-instruktioner `Startup.cs`i.

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Lägg till följande i slutet av ConfigureServices-metoden i `Startup` -klassen i. `Startup.cs`

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Om [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paket version 1.3.4 eller nedan används, lägger du till följande using-instruktioner `Startup.cs`i.

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Lägg till följande `SnapshotCollectorTelemetryProcessorFactory` klassen till `Startup` klass.
    
       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;
    
               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;
    
               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Lägg till den `SnapshotCollectorConfiguration` och `SnapshotCollectorTelemetryProcessorFactory` tjänster till Start-pipelinen:
    
        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));
    
               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));
    
               // TODO: Add other services your application needs here.
           }
       }
       ```

4. Vid behov kan du anpassa Snapshot Debugger-konfigurationen genom att lägga till ett SnapshotCollectorConfiguration-avsnitt till appSettings. JSON. Alla inställningar i Snapshot Debugger konfigurationen är valfria. Här är ett exempel som visar en konfiguration som motsvarar standard konfigurationen:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurera insamling av ögonblicksbild för andra .NET-program

1. Om programmet inte redan har instrumenterats med Application Insights, Kom igång genom att [att aktivera Application Insights och ange instrumenteringsnyckeln](../../azure-monitor/app/windows-desktop.md).

2. Lägg till den [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-paketet i din app.

3. Ögonblicksbilder samlas endast på undantag som har rapporterats till Application Insights. Du kan behöva ändra koden för att rapportera dem. Undantagshantering kod är beroende av strukturen för ditt program, men ett exempel är lägre än:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program som kan utlösa ett undantag. Vänta sedan 10 till 15 minuter för ögonblicks bilder som ska skickas till Application Insights-instansen.
- Se [ögonblicks bilder](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) i Azure Portal.
- Hjälp med fel sökning Snapshot Debugger problem finns i [Snapshot debugger fel sökning](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
