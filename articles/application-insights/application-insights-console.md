---
title: Azure Application Insights för konsolprogram | Microsoft Docs
description: Övervaka webbprogram för tillgänglighet, prestanda och användning.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2018
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: b2b5abf3b3b170e60df3aa2d6ec5ce471db74f80
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42061094"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights för .NET-konsolprogram
[Application Insights](app-insights-overview.md) kan du övervaka ditt webbprogram för tillgänglighet, prestanda och användning.

Du behöver en prenumeration med [Microsoft Azure](http://azure.com). Logga in med ett Microsoft-konto som du kanske har för Windows, Xbox Live eller andra Microsoft-molntjänster. Ditt team kan ha en organisationens prenumeration på Azure: ber du ägaren att lägga till dig till den med ditt Microsoft-konto.

## <a name="getting-started"></a>Komma igång

* [Skapa en Application Insights-resurs](app-insights-create-new-resource.md) på [Azure Portal](https://portal.azure.com). Programtyp, Välj **Allmänt**.
* Kopiera instrumenteringsnyckeln. Leta reda på nyckeln i den **Essentials** listrutan för den nya resursen som du skapade. 
* Installera senaste [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) paketet.
* Ange instrumenteringsnyckeln i din kod innan du spåra någon telemetri (eller uppsättning APPINSIGHTS_INSTRUMENTATIONKEY miljövariabeln). Efter det ska du kunna spåra telemetri och se hur det på Azure portal manuellt

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Installera senaste versionen av [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) paket - spårar automatiskt HTTP, SQL eller några andra externa beroendeanrop.

Du kan starta och konfigurera Application Insights från koden eller med hjälp av `ApplicationInsights.config` fil. Kontrollera att initieringen händer så tidigt som möjligt. 

> [!NOTE]
> Instruktioner som refererar till **ApplicationInsights.config** gäller endast för appar som riktar in sig på .NET Standard och gäller inte för .NET Core-program. 

### <a name="using-config-file"></a>Med hjälp av konfigurationsfilen

Som standard söker Application Insights SDK `ApplicationInsights.config` filen i arbetskatalogen när `TelemetryConfiguration` håller på att skapas

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Du kan också ange sökvägen till konfigurationsfilen.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Mer information finns i [configuration filreferens](app-insights-configuration-with-applicationinsights-config.md).

Du kan få ett fullständigt exempel av konfigurationsfilen genom att installera senaste versionen av [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) paketet. Här är den **minimal** konfiguration för beroende samling som motsvarar kodexemplet.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
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

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurera telemetriinsamling av från kod

* Under start av program skapar och konfigurerar `DependencyTrackingTelemetryModule` instans - det måste vara singleton och måste bevaras under programmet livstid.

```csharp
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

* Lägg till vanliga telemetri-initierare

```csharp
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* För .NET Framework Windows-app måste du även installera och initiera prestandaräknaren insamlaren modulen enligt beskrivningen [här](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

#### <a name="full-example"></a>Fullständigt exempel

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.Active;

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient();
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

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
    }
}

```

## <a name="next-steps"></a>Nästa steg
* [Övervaka beroenden](app-insights-asp-net-dependencies.md) att se om REST, SQL eller andra externa resurser gör systemet långsammare.
* [Använda API: et](app-insights-api-custom-events-metrics.md) att skicka dina egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.
