---
title: ApplicationInsights.config - referens i Azure | Microsoft Docs
description: "Aktivera eller inaktivera data collection moduler och lägga till prestandaräknare och andra parametrar."
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
editor: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: e59df358f25663c742b0da09cf27b974787536dc
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurera Application Insights SDK:n med ApplicationInsights.config eller .xml
Application Insights .NET SDK består av ett antal NuGet-paket. Den [core-paketet](http://www.nuget.org/packages/Microsoft.ApplicationInsights) innehåller API: et för att skicka telemetri till Application Insights. [Ytterligare paket](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) ange telemetri *moduler* och *initierare* för automatiskt spåra telemetri från ditt program och dess kontext. Genom att justera konfigurationsfilen kan du aktivera eller inaktivera telemetri moduler och initierare och ange parametrar för några av dem.

Konfigurationsfilen har namnet `ApplicationInsights.config` eller `ApplicationInsights.xml`, beroende på vilken typ av ditt program. Läggs automatiskt till ditt projekt när du [installera de flesta versioner av SDK][start]. Läggs också till en webbapp med [Status Monitor på en IIS-server][redfield], eller när du väljer Appplication Insights [tillägg för Azure-webbplats eller VM](app-insights-azure-web-apps.md).

Det finns inte en motsvarande fil att styra den [SDK på en webbsida][client].

Det här dokumentet beskrivs i avsnitt som du ser i konfigurationen fil, hur de styra komponenter av SDK, och vilka NuGet-paket att läsa in dessa komponenter.

## <a name="telemetry-modules-aspnet"></a>Telemetri moduler (ASP.NET)
Varje telemetri modul samlar in en viss typ av data och använder core API för att skicka data. Modulerna som installeras av olika NuGet-paket, som också lägga till raderna som behövs i .config-filen.

Det finns en nod i konfigurationsfilen för varje modul. Om du vill inaktivera en modul, ta bort noden eller kommentera ut.

### <a name="dependency-tracking"></a>Beroende spårning
[Beroende spårning](app-insights-asp-net-dependencies.md) samlar in telemetri om anrop appen blir för databaser och externa tjänster och -databaser. Om du vill att denna modul ska fungera i en IIS-server, måste du [installera statusövervakaren][redfield]. Använda den i Azure-webbappar eller virtuella datorer, [markerar du Application Insights-tillägget](app-insights-azure-web-apps.md).

Du kan också skriva egna beroende spårning kod med hjälp av den [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet-paketet.

### <a name="performance-collector"></a>Insamlare för prestanda
[Samlar in prestandaräknare för system](app-insights-performance-counters.md) t.ex CPU, minne och nätverk läses in från IIS-installationer. Du kan ange vilka räknare som ska samlas in, inklusive prestandaräknare som du har skapat själv.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet-paketet.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights-Diagnostiktelemetri
Den `DiagnosticsTelemetryModule` rapporterar fel i själva koden för instrumentation Application Insights. Till exempel om koden inte kan komma åt prestandaräknare eller om en `ITelemetryInitializer` genererar ett undantag. Spårningstelemetri spåras av den här modulen visas i den [diagnostiska Sök][diagnostic]. Skickar diagnostikdata till dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-paketet. Om du endast installera det här paketet skapas inte filen applicationinsights.config automatiskt.

### <a name="developer-mode"></a>Utvecklarläge
`DeveloperModeWithDebuggerAttachedTelemetryModule`Tvingar Application Insights `TelemetryChannel` att skicka data direkt, en telemetri objekt samtidigt, när en felsökare är kopplad till programmet. Detta minskar den tid ögonblick när programmet spårar telemetri och när den visas i Application Insights-portalen. Det medför betydande kostnader i CPU: N och bandbredd.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-paketet

### <a name="web-request-tracking"></a>Webbegäran spårning
Rapporter i [tid och resultatet svarskoden](app-insights-asp-net.md) av HTTP-begäranden.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-paketet

### <a name="exception-tracking"></a>Undantagsspårning
`ExceptionTrackingTelemetryModule`spårar ohanterade undantag i ditt webbprogram. Se [fel och undantag][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-paketet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-spårar [symptom uppgiften undantag](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-spårar ohanterade undantag för arbetsroller, windows-tjänster och program.
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-paketet.

### <a name="eventsource-tracking"></a>EventSource spårning
`EventSourceTelemetryModule`kan du konfigurera EventSource händelser skickas till Application Insights som spårningar. Information om hur du spårar EventSource händelser finns [med EventSource händelser](app-insights-asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Spårning av ETW-händelse
`EtwCollectorTelemetryModule`kan du konfigurera händelser från ETW-providers som ska skickas till Application Insights som spårningar. Information om hur du spårar ETW-händelser finns i [med hjälp av ETW-händelser](app-insights-asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Microsoft.ApplicationInsights erbjuder den [core API](https://msdn.microsoft.com/library/mt420197.aspx) av SDK. Använder informationen för de andra telemetri modulerna, och du kan också [använda den för att definiera egna telemetri](app-insights-api-custom-events-metrics.md).

* Ingen post i ApplicationInsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-paketet. Om du bara installera den här NuGet skapas ingen .config-fil.

## <a name="telemetry-channel"></a>Telemetri kanal
Kanalen telemetri hanterar buffring och överföring av telemetri till Application Insights-tjänsten.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`är standardkanalen för tjänster. Den buffrar data i minnet.
* `Microsoft.ApplicationInsights.PersistenceChannel`är ett alternativ för konsolprogram. Det kan spara unflushed data till beständig lagring när din app stängs och skicka den när appen startar igen.

## <a name="telemetry-initializers-aspnet"></a>Telemetri initierare (ASP.NET)
Telemetri initierare ange kontextegenskaper för som skickas tillsammans med alla element på telemetri.

Du kan [skriva egna initierare](app-insights-api-filtering-sampling.md#add-properties) att ange kontextegenskaper.

Standard initierare anges alla antingen genom webb- eller WindowsServer NuGet-paket:

* `AccountIdTelemetryInitializer`Egenskapen AccountId.
* `AuthenticatedUserIdTelemetryInitializer`Egenskapen AuthenticatedUserId som angetts av JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer`uppdateringar av `RoleName` och `RoleInstance` egenskaper för den `Device` kontext för alla telemetri artiklar med information som hämtas från Azure körningsmiljön.
* `BuildInfoConfigComponentVersionTelemetryInitializer`uppdateringar av `Version` -egenskapen för den `Component` kontext för alla telemetri objekt med värdet hämtas från den `BuildInfo.config` fil produceras av MS-Build.
* `ClientIpHeaderTelemetryInitializer`uppdateringar `Ip` -egenskapen för den `Location` kontexten för alla telemetri objekt baserat på den `X-Forwarded-For` HTTP-huvudet i begäran.
* `DeviceTelemetryInitializer`följande egenskaper för den `Device` kontext för alla telemetri objekt.
  * `Type`anges till ”dator”
  * `Id`anges till domännamnet för datorn där webbprogrammet körs.
  * `OemName`anges till värdet hämtas från den `Win32_ComputerSystem.Manufacturer` med hjälp av WMI.
  * `Model`anges till värdet hämtas från den `Win32_ComputerSystem.Model` med hjälp av WMI.
  * `NetworkType`anges till värdet hämtas från den `NetworkInterface`.
  * `Language`Ange namnet på den `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer`uppdateringar av `RoleInstance` -egenskapen för den `Device` kontext för alla telemetri artiklar med domännamnet på den dator där webbprogrammet körs.
* `OperationNameTelemetryInitializer`uppdateringar av `Name` -egenskapen för den `RequestTelemetry` och `Name` -egenskapen för den `Operation` kontexten för alla telemetri objekt baserat på HTTP-metoden, samt namnen på ASP.NET MVC-kontrollanten och åtgärden anropas för att bearbeta begäran.
* `OperationIdTelemetryInitializer`eller `OperationCorrelationTelemetryInitializer` uppdateringar av `Operation.Id` kontextegenskap telemetri postobjekt spåras när en begäran med den automatiskt genererade `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`uppdateringar av `Id` -egenskapen för den `Session` kontext för alla telemetri objekt med värdet som har extraherats från den `ai_session` cookie som genererats av ApplicationInsights JavaScript instrumentation koden körs i webbläsaren.
* `SyntheticTelemetryInitializer`eller `SyntheticUserAgentTelemetryInitializer` uppdateringar av `User`, `Session` och `Operation` kontexter egenskaper för alla objekt i telemetri spåras vid hantering av en begäran från en syntetisk källa, t.ex. en tillgänglighet testa eller Sök motorn bot. Som standard [Metrics Explorer](app-insights-metrics-explorer.md) visar inte syntetiska telemetri.

    Den `<Filters>` ange identifierar egenskaperna för begäranden.
* `UserAgentTelemetryInitializer`uppdateringar av `UserAgent` -egenskapen för den `User` kontexten för alla telemetri objekt baserat på den `User-Agent` HTTP-huvudet i begäran.
* `UserTelemetryInitializer`uppdateringar av `Id` och `AcquisitionDate` egenskaper för `User` kontext för alla telemetri objekt med värden som extraheras från den `ai_user` cookie som genererats av Application Insights JavaScript instrumentation koden körs i användarens webbläsare.
* `WebTestTelemetryInitializer`Anger användar-id, sessions-id och syntetiska datakällans egenskaper för HTTP-begäranden som kommer från [tillgänglighetstester](app-insights-monitor-web-app-availability.md).
  Den `<Filters>` ange identifierar egenskaperna för begäranden.

För .NET-program som körs i Service Fabric kan du inkludera den `Microsoft.ApplicationInsights.ServiceFabric` NuGet-paketet. Det här paketet innehåller en `FabricTelemetryInitializer`, som lägger till Service Fabric egenskaper telemetri objekt. Mer information finns i [GitHub-sidan](https://go.microsoft.com/fwlink/?linkid=848457) om de egenskaper som lagts till av den här NuGet-paketet.

## <a name="telemetry-processors-aspnet"></a>Telemetri processorer (ASP.NET)
Telemetri processorer kan filtrera och ändra varje telemetri objekt precis innan den skickas till portalen från SDK.

Du kan [skriva telemetri processorerna](app-insights-api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Anpassningsbar provtagning telemetri processor (från 2.0.0-beta3)
Den här funktionen är aktiverad som standard. Om appen skickar en mängd telemetri, denna processor tar bort vissa av den.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametern innehåller de mål som algoritmen försöker nå. Varje instans av SDK fungerar oberoende av varandra, så om din server är ett kluster på flera datorer, den faktiska datavolym telemetri ska multipliceras därefter.

[Lär dig mer om sampling](app-insights-sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Fast räntesats provtagning telemetri processor (från 2.0.0-beta1)
Det finns också en standard [provtagning telemetri processor](app-insights-api-filtering-sampling.md) (från 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Kanalparametrar (Java)
Dessa parametrar påverkar hur Java SDK ska lagra och rensa den samlar in telemetridata.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Antal telemetri objekt som kan lagras i SDK: ns InMemory-lagringen. När antalet har uppnåtts telemetri bufferten töms - som är telemetri objekt skickas till Application Insights-servern.

* Min: 1
* Max: 1000
* Standard: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Anger hur ofta data som lagras i InMemory-lagringen ska tas bort (skickat till Application Insights).

* Min: 1
* Max: 300
* Standard: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Anger den maximala storleken i MB som har tilldelats till den beständiga lagringen på den lokala disken. Den här används för bestående telemetri-objekt som inte kunde överföras till Application Insights-slutpunkten. När lagringsstorleken har uppfyllts, ignoreras nya telemetri objekt.

* Min: 1
* Max: 100
* Standard: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey
Detta avgör Application Insights-resurs där dina data visas. Normalt skapar du en separat resurs med en separat nyckel för var och en av dina program.

Om du vill ange nyckeln dynamiskt – till exempel om du vill skicka resultaten från ditt program till olika resurser - kan du utelämna nyckeln från konfigurationsfilen och ange i koden i stället.

Om du vill ange nyckeln för alla instanser av TelemetryClient, ange inklusive standard telemetri moduler, nyckeln i TelemetryConfiguration.Active. Gör så här i en initieringsmetod, till exempel global.aspx.cs i en ASP.NET-tjänst:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Om du vill skicka en viss uppsättning händelser till en annan resurs anger du nyckeln för en specifik TelemetryClient:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Få en ny nyckel [skapar en ny resurs i Application Insights-portalen][new].

## <a name="next-steps"></a>Nästa steg
[Mer information om API: et][api].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
