---
title: Azure Application Insights for Console Applications | Microsoft-dokument
description: Övervaka webbprogram för tillgänglighet, prestanda och användning.
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: baaea0f8055eeff0314fcf5fde00729ea8091d12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655437"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights för .NET-konsolprogram

[Med Application Insights](../../azure-monitor/app/app-insights-overview.md) kan du övervaka ditt webbprogram för tillgänglighet, prestanda och användning.

Du behöver en prenumeration med [Microsoft Azure](https://azure.com). Logga in med ett Microsoft-konto som du kan ha för Windows, Xbox Live eller andra Microsoft-molntjänster. Ditt team kan ha en organisationsprenumeration på Azure: be ägaren att lägga till dig i det med ditt Microsoft-konto.

> [!NOTE]
> Det finns en ny Application Insights SDK kallas [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) som kan användas för att aktivera Application Insights för alla konsolprogram. Vi rekommenderar att du använder detta paket och tillhörande instruktioner [härifrån](../../azure-monitor/app/worker-service.md). Det här [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)paketet är inriktat på och kan därför användas i .NET Core 2.0 eller senare och .NET Framework 4.7.2 eller senare.

## <a name="getting-started"></a>Komma igång

* [Skapa en Application Insights-resurs](../../azure-monitor/app/create-new-resource.md) på [Azure Portal](https://portal.azure.com). För programtyp väljer du **Allmänt**.
* Kopiera instrumenteringsnyckeln. Hitta nyckeln i listrutan **Essentials** för den nya resursen som du har skapat.
* Installera det senaste [Microsoft.ApplicationInsights-paketet.](https://www.nuget.org/packages/Microsoft.ApplicationInsights)
* Ställ in instrumenteringsnyckeln i koden innan du spårar någon telemetri (eller ställer in APPINSIGHTS_INSTRUMENTATIONKEY miljövariabel). Därefter bör du kunna spåra telemetri manuellt och se den på Azure-portalen

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetri skickas inte direkt. Telemetriartiklar batchas och skickas av ApplicationInsights SDK. I Console-appar, som avslutas direkt efter att du `Track()` har `Flush()` `Sleep` ringt metoder, kan telemetri inte skickas om och göras innan appen avslutas som visas i [fullt exempel](#full-example) senare i den här artikeln.


* Installera den senaste versionen av [Paketet Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) – http-, SQL- eller något annat externt beroendeanrop spårar automatiskt HTTP,SQL eller andra externa beroendeanrop.

Du kan initiera och konfigurera Application Insights `ApplicationInsights.config` från koden eller använda filen. Se till att initiering sker så tidigt som möjligt. 

> [!NOTE]
> Instruktioner som refererar till **ApplicationInsights.config** är endast tillämpliga på appar som är inriktade på .NET Framework och gäller inte för .NET Core-program.

### <a name="using-config-file"></a>Använda config-fil

Som standard söker `ApplicationInsights.config` Application Insights SDK efter `TelemetryConfiguration` filen i arbetskatalogen när den skapas

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Du kan också ange sökvägen till konfigurationsfilen.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Mer information finns i [referens för konfigurationsfil](configuration-with-applicationinsights-config.md).

Du kan få ett fullständigt exempel på config-filen genom att installera den senaste versionen av [Microsoft.ApplicationInsights.WindowsServer-paketet.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) Här är den **minimala** konfigurationen för beroendesamling som motsvarar kodexemplet.

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

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurera telemetrisamling från kod
> [!NOTE]
> Läsa config-fil stöds inte på .NET Core. Du kan överväga att använda [Application Insights SDK för ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Under programstart skapa och `DependencyTrackingTelemetryModule` konfigurera instans - det måste vara singleton och måste bevaras för programmets livstid.

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

* Lägg till vanliga telemetriinitierare

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Om du har `TelemetryConfiguration()` skapat konfiguration med vanlig konstruktor måste du aktivera korrelationsstöd ytterligare. **Det behövs inte** om du läser `TelemetryConfiguration.CreateDefault()` konfiguration `TelemetryConfiguration.Active`från fil, används eller .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Du kanske också vill installera och initiera Performance Counter collector modul som beskrivs [här](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


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

            var telemetryClient = new TelemetryClient(configuration);
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
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

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
* [Övervaka beroenden](../../azure-monitor/app/asp-net-dependencies.md) för att se om REST, SQL eller andra externa resurser saktar ner dig.
* [Använd API:et](../../azure-monitor/app/api-custom-events-metrics.md) för att skicka dina egna händelser och mått för en mer detaljerad vy över appens prestanda och användning.
