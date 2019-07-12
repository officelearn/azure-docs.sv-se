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
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 0c2a28462633d47ad1d3f247793e3fcf6f4d40c0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795452"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights för .NET-konsolprogram
[Application Insights](../../azure-monitor/app/app-insights-overview.md) kan du övervaka ditt webbprogram för tillgänglighet, prestanda och användning.

Du behöver en prenumeration med [Microsoft Azure](https://azure.com). Logga in med ett Microsoft-konto som du kanske har för Windows, Xbox Live eller andra Microsoft-molntjänster. Ditt team kan ha en organisationens prenumeration på Azure: ber du ägaren att lägga till dig till den med ditt Microsoft-konto.

## <a name="getting-started"></a>Komma igång

* [Skapa en Application Insights-resurs](../../azure-monitor/app/create-new-resource.md) på [Azure Portal](https://portal.azure.com). Programtyp, Välj **Allmänt**.
* Kopiera instrumenteringsnyckeln. Leta reda på nyckeln i den **Essentials** listrutan för den nya resursen som du skapade. 
* Installera senaste [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) paketet.
* Ange instrumenteringsnyckeln i din kod innan du spåra någon telemetri (eller uppsättning APPINSIGHTS_INSTRUMENTATIONKEY miljövariabeln). Efter det ska du kunna spåra telemetri och se hur det på Azure portal manuellt

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* Installera senaste versionen av [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) paket - spårar automatiskt HTTP, SQL eller några andra externa beroendeanrop.

Du kan starta och konfigurera Application Insights från koden eller med hjälp av `ApplicationInsights.config` fil. Kontrollera att initieringen händer så tidigt som möjligt. 

> [!NOTE]
> Instruktioner som refererar till **ApplicationInsights.config** gäller endast för appar som riktar in sig på .NET Framework och gäller inte för .NET Core-program.

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

Mer information finns i [configuration filreferens](configuration-with-applicationinsights-config.md).

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
> [!NOTE]
> Läsa konfigurationsfilen stöds inte på .NET Core. Du kan överväga att använda [Application Insights SDK för ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Under start av program skapar och konfigurerar `DependencyTrackingTelemetryModule` instans - det måste vara singleton och måste bevaras under programmet livstid.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Lägg till vanliga telemetri-initierare

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Om du har skapat konfigurationen med oformaterad `TelemetryConfiguration()` konstruktor, måste du aktivera stöd för korrelation dessutom. **Den krävs inte** om du läser konfigurationen från filen används `TelemetryConfiguration.CreateDefault()` eller `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Du kanske också vill installera och initiera prestandaräknaren insamlaren modulen enligt beskrivningen [här](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


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
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
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
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

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
* [Övervaka beroenden](../../azure-monitor/app/asp-net-dependencies.md) att se om REST, SQL eller andra externa resurser gör systemet långsammare.
* [Använda API: et](../../azure-monitor/app/api-custom-events-metrics.md) att skicka dina egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.
