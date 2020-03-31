---
title: Aktivera felsökare för ögonblicksbilder för .NET-appar i Azure Service Fabric, Cloud Service och Virtual Machines | Microsoft-dokument
description: Aktivera felsökare för ögonblicksbilder för .NET-appar i Azure Service Fabric, Cloud Service och Virtual Machines
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671359"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Aktivera felsökare för ögonblicksbilder för .NET-appar i Azure Service Fabric, Cloud Service och Virtual Machines

Om ditt ASP.NET eller ASP.NET kärnprogram körs i Azure App Service rekommenderar vi starkt att [ögonblicksbildfelsökaren aktiveras via portalsidan application insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Men om ditt program kräver en anpassad snapshot debugger konfiguration, eller en förhandsgranskning version av .NET core, då denna instruktion bör följas ***utöver*** instruktionerna för [att aktivera via Application Insights portal sidan](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Om ditt program körs i Azure Service Fabric, Cloud Service, Virtuella datorer eller lokala datorer bör följande instruktioner användas. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurera ögonblicksbildsamling för ASP.NET program

1. [Aktivera application insights i din webbapp](../../azure-monitor/app/asp-net.md), om du inte har gjort det ännu.

2. Inkludera [paketet Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet i appen.

3. Om det behövs har den snapshot-felsökningskonfiguration som lagts till [i ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Standardkonfigurationen för ögonblicksbildfelsökning är oftast tom och alla inställningar är valfria. Här är ett exempel som visar en konfiguration som motsvarar standardkonfigurationen:

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

4. Ögonblicksbilder samlas endast in på undantag som rapporteras till Application Insights. I vissa fall (till exempel äldre versioner av .NET-plattformen) kan du behöva [konfigurera undantagssamling](../../azure-monitor/app/asp-net-exceptions.md#exceptions) för att se undantag med ögonblicksbilder i portalen.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Konfigurera ögonblicksbildsamling för program som använder ASP.NET Core 2.0 eller senare

1. [Aktivera Application Insights i din ASP.NET Core-webbapp](../../azure-monitor/app/asp-net-core.md), om du inte har gjort det ännu.

    > [!NOTE]
    > Se till att programmet refererar till version 2.1.1, eller nyare, av Microsoft.ApplicationInsights.AspNetCore-paketet.

2. Inkludera [paketet Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet i appen.

3. Ändra programmets `Startup` klass för att lägga till och konfigurera Snapshot Collectors telemetriprocessor.
    1. Om [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet paketversion 1.3.5 eller högre används, `Startup.cs`lägger du till följande med hjälp av satser i .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Lägg till följande i slutet av metoden `Startup` ConfigureServices i klassen i `Startup.cs`.

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Om [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet paketversion 1.3.4 eller lägre används, `Startup.cs`lägger du till följande med hjälp av satser i .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Lägg till `SnapshotCollectorTelemetryProcessorFactory` följande `Startup` klass i klassen.
    
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
        Lägg `SnapshotCollectorConfiguration` till `SnapshotCollectorTelemetryProcessorFactory` och tjänster i startpipelinen:
    
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

4. Om det behövs anpassade du snapshot-felsökningskonfigurationen genom att lägga till ett snapshotcollectorConfiguration-avsnitt i appsettings.json. Alla inställningar i snapshot-felsökningskonfigurationen är valfria. Här är ett exempel som visar en konfiguration som motsvarar standardkonfigurationen:

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

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurera ögonblicksbildsamling för andra .NET-program

1. Om ditt program inte redan är instrumenterat med Application Insights kan du komma igång genom [att aktivera Application Insights och ställa in instrumenteringsnyckeln](../../azure-monitor/app/windows-desktop.md).

2. Lägg till [paketet Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet i appen.

3. Ögonblicksbilder samlas endast in på undantag som rapporteras till Application Insights. Du kan behöva ändra koden för att rapportera dem. Undantagshanteringskoden beror på programmets struktur, men ett exempel finns nedan:
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

- Generera trafik till ditt program som kan utlösa ett undantag. Vänta sedan 10 till 15 minuter på att ögonblicksbilder ska skickas till application insights-instansen.
- Se [ögonblicksbilder](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) i Azure-portalen.
- Mer information om felsökning av felsökare i ögonblicksbilder finns i [felsökning av ögonblicksbildfelsökning](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
