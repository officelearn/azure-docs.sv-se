---
title: "Azure Application Insights för konsolprogram | Microsoft Docs"
description: "Övervaka webbprogram för tillgänglighet, prestanda och användning."
services: application-insights
documentationcenter: .net
author: lmolkova
manager: bfung
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 1b6bef88d729a2febfca2bd236a5382d2c11bd69
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights för .NET konsolen program
[Application Insights](app-insights-overview.md) kan du övervaka ditt webbprogram för tillgänglighet, prestanda och användning.

Du behöver en prenumeration med [Microsoft Azure](http://azure.com). Logga in med ett Microsoft-konto som du kanske för Windows, Xbox Live och andra Microsoft-molntjänster. Gruppen kan ha en organisationens prenumeration på Azure: Be ägare att lägga till dig i den med ditt Microsoft-konto.

## <a name="getting-started"></a>Komma igång

* [Skapa en Application Insights-resurs](app-insights-create-new-resource.md) på [Azure Portal](https://portal.azure.com). Välj ASP.NET-app för programtyp.
* Kopiera instrumenteringsnyckeln. Hitta nyckeln i Essentials listrutan för den nya resursen som du skapade. 
* Installera senaste [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) paketet.
* Ange nyckeln instrumentation i koden innan spåra alla telemetri (eller uppsättning APPINSIGHTS_INSTRUMENTATIONKEY miljövariabeln). Efter att ska du kunna spåra telemetri och finns på Azure-portalen manuellt

```C#
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Installera senaste versionen av [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) paket - spåras automatiskt HTTP, SQL och vissa andra externa beroendeanrop.

Du kan starta och konfigurera Application Insights från koden eller med hjälp av `ApplicationInsights.config` fil. Kontrollera att initiering sker så snart som möjligt.

### <a name="using-config-file"></a>Använda konfigurationsfilen

Som standard söker Application Insights SDK `ApplicationInsights.config` filen i arbetskatalogen när `TelemetryConfiguration` håller på att skapas

```C#
TelemetryConfiguration config = TelemetryConfiguration.Active; // Read ApplicationInsights.config file if present
```

Du kan också ange sökväg till konfigurationsfilen.

```C#
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration("ApplicationInsights.config");
```

Mer information finns i [configuration filreferens](app-insights-configuration-with-applicationinsights-config.md).

Du kan få ett fullständigt exempel på konfigurationsfilen genom att installera senaste versionen av [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) paketet. Här är den **minimal** konfiguration för beroende samling som motsvarar kodexemplet.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurera telemetri insamling från kod

* Vid start av program skapa och konfigurera `DependencyTrackingTelemetryModule` instans - det måste vara singleton och måste bevaras för programmet livstid.

```C#
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Lägg till vanliga telemetri initierare

```C#
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* För .NET Framework Windows-appen du även installera och initiera prestandaräknaren insamlaren modulen enligt [här](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

#### <a name="full-example"></a>Fullständigt exempel

```C#
static void Main(string[] args)
{
    TelemetryConfiguration configuration = TelemetryConfiguration.Active;

    configuration.InstrumentationKey = "removed";
    configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
    configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

    var telemetryClient = new TelemetryClient();
    using (IntitializeDependencyTracking(configuration))
    {
        telemetryClient.TrackTrace("Hello World!");

        using (var httpClient = new HttpClient())
        {
            // Http dependency is automatically tracked!
            httpClient.GetAsync("https://microsoft.com").Wait();
        }
    }

    // run app...

    // when application stops or you are done with dependency tracking, do not forget to dispose the module
    dependencyTrackingModule.Dispose();

    telemetryClient.Flush();
}

static DependencyTrackingTelemetryModule IntitializeDependencyTracking(TelemetryConfiguration configuration)
{
    // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");    
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

    // enable known dependency tracking, note that in future versions, we will extend this list. 
    // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

    // initialize the module
    module.Initialize(configuration);

    return module;
}
```

## <a name="next-steps"></a>Nästa steg
* [Övervaka beroenden](app-insights-asp-net-dependencies.md) att se om REST, SQL eller andra externa resurser saktar ner dig.
* [Använd API](app-insights-api-custom-events-metrics.md) att skicka dina egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.
