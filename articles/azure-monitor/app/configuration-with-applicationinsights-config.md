---
title: Referens för ApplicationInsights.config – Azure | Microsoft Docs
description: Aktivera eller inaktivera data collection moduler och lägga till prestandaräknare och andra parametrar.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 3c7b855588b45cd61bb13848720d6e926bbecba4
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121115"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurera Application Insights SDK:n med ApplicationInsights.config eller .xml
Application Insights .NET SDK består av ett antal NuGet-paket. Den [core-paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights) tillhandahåller API: et för att skicka telemetri till Application Insights. [Ytterligare paket](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) ger telemetri *moduler* och *fältparameterbindningar* för att spåra automatiskt telemetri från ditt program och dess kontext. Genom att justera konfigurationsfilen kan du aktivera eller inaktivera telemetri moduler och initierare och ställa in parametrar för några av dem.

Konfigurationsfilen har namnet `ApplicationInsights.config` eller `ApplicationInsights.xml`, beroende på vilken typ av ditt program. Läggs automatiskt till ditt projekt när du [installera de flesta versioner av SDK][start]. Det läggs också till en webbapp med [statusövervakaren på en IIS-server][redfield], eller när du väljer Application Insights [-tillägg för en Azure-webbplats eller virtuell dator](azure-web-apps.md).

Det finns inte en motsvarande fil att styra den [SDK på en webbsida][client].

Det här dokumentet beskrivs i avsnitt som du ser i konfigurationen fil, hur de kontrollera komponenterna i SDK, och vilka NuGet-paket läsa in dessa komponenter.

> [!NOTE]
> ApplicationInsights.config och .xml instruktioner gäller inte för .NET Core SDK. Ändringar till en .NET Core-program använder vi vanligtvis filen appsettings.json. Ett exempel på detta finns i den [Snapshot Debugger-dokumentationen.](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)

## <a name="telemetry-modules-aspnet"></a>Telemetri moduler (ASP.NET)
Varje modul telemetri samlar in en viss typ av data och använder core API för att skicka data. Modulerna som installeras av olika NuGet-paket, som också lägga till raderna som behövs i .config-filen.

Det finns en nod i konfigurationsfilen för varje modul. Om du vill inaktivera en modul, ta bort noden eller kommentera ut.

### <a name="dependency-tracking"></a>Beroendespårning
[Beroendespårning](../../azure-monitor/app/asp-net-dependencies.md) samlar in telemetri om anrop som din app som gör att databaser och externa tjänster och -databaser. Om du vill tillåta den här modulen ska fungera i en IIS-server, måste du [installera Status Monitor][redfield]. Kan använda dem med Azure web apps eller virtuella datorer, [Välj Application Insights-tillägget](azure-web-apps.md).

Du kan också skriva egna beroendespårning kod med hjälp av den [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet-paketet.

### <a name="performance-collector"></a>Insamlare för prestanda
[Samlar in systemprestandaräknare](../../azure-monitor/app/performance-counters.md) t.ex. CPU, minne och Läs in från en IIS-installation. Du kan ange vilka räknare som ska samlas in, inklusive prestandaräknare som du har installerat själv.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet-paketet.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights-Diagnostiktelemetri
Den `DiagnosticsTelemetryModule` rapporterar fel i själva koden berörs instrumentation Application Insights. Till exempel om koden inte kan komma åt prestandaräknare eller om en `ITelemetryInitializer` genereras ett undantag. Spårningstelemetri spåras av den här modulen visas i den [Diagnostiksökning][diagnostic]. Skickar diagnostikdata till dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-paketet. Om du bara installera det här paketet skapas inte filen ApplicationInsights.config automatiskt.

### <a name="developer-mode"></a>Utvecklarläget
`DeveloperModeWithDebuggerAttachedTelemetryModule` Tvingar Application Insights `TelemetryChannel` att skicka data direkt, en telemetriobjekt i taget, när en felsökare är kopplad till programprocessen. Detta minskar mängden mellan när programmet spårar telemetri och när den visas på Application Insights-portalen. Det gör betydande overhead i processor- och bandbredd.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-paketet

### <a name="web-request-tracking"></a>Webbegäran spårning
Rapporter i [tid och resultatet svarskod](../../azure-monitor/app/asp-net.md) för HTTP-begäranden.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-paketet

### <a name="exception-tracking"></a>Undantagsspårning
`ExceptionTrackingTelemetryModule` spårar ohanterade undantag i webbappen. Se [fel och undantag][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet-paketet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -spår [symptom uppgift undantag](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -spårar ohanterade undantag för worker-roller, windows-tjänster och -konsolprogram.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-paketet.

### <a name="eventsource-tracking"></a>EventSource spårning
`EventSourceTelemetryModule` kan du konfigurera EventSource händelser som ska skickas till Application Insights som spårningar. Information om spårning av EventSource händelser finns i [med EventSource händelser](../../azure-monitor/app/asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW-händelsespårning
`EtwCollectorTelemetryModule` kan du konfigurera händelser från ETW-leverantörer som ska skickas till Application Insights som spårningar. Information om hur du spårar ETW-händelser finns i [med ETW-händelser](../../azure-monitor/app/asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Microsoft.ApplicationInsights paketet innehåller den [core API](https://msdn.microsoft.com/library/mt420197.aspx) av SDK. De andra telemetri-modulerna använder detta, och du kan också [använda den för att definiera din egen telemetri](../../azure-monitor/app/api-custom-events-metrics.md).

* Ingen post i ApplicationInsights.config.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-paketet. Om du bara installera den här NuGet, skapas ingen .config-filen.

## <a name="telemetry-channel"></a>Telemetri kanal
Telemetri kanalen hanterar buffring och överföring av telemetri till Application Insights-tjänsten.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` är standardkanal för tjänster. Den buffrar data i minnet.
* `Microsoft.ApplicationInsights.PersistenceChannel` är ett alternativ för konsolprogram. Det kan spara unflushed data till beständig lagring när din app stängs och skicka den när appen startar igen.

## <a name="telemetry-initializers-aspnet"></a>Telemetri-initierare (ASP.NET)
Telemetri-initierare ange kontextegenskaperna som skickas tillsammans med alla objekt i telemetrin.

Du kan [skriva din egen initieringar](../../azure-monitor/app/api-filtering-sampling.md#add-properties) att ställa in kontextegenskaperna.

Standard initierarna är allt klart antingen genom webb- eller WindowsServer NuGet-paket:

* `AccountIdTelemetryInitializer` Anger AccountId-egenskapen.
* `AuthenticatedUserIdTelemetryInitializer` Anger AuthenticatedUserId egenskapen som angetts av JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer` uppdateringar i `RoleName` och `RoleInstance` egenskaperna för den `Device` kontext för alla telemetri artiklar med information som hämtas från Azure-körningsmiljön.
* `BuildInfoConfigComponentVersionTelemetryInitializer` uppdateringar i `Version` egenskapen för den `Component` kontext för alla telemetri-objekt med värdet som extraheras från den `BuildInfo.config` filen som genereras av MS Build.
* `ClientIpHeaderTelemetryInitializer` uppdateringar `Ip` egenskapen för den `Location` kontext för alla objekt som telemetri baserat på den `X-Forwarded-For` HTTP-huvud för begäran.
* `DeviceTelemetryInitializer` följande egenskaper för den `Device` kontext för alla objekt som telemetri.
  * `Type` anges till ”PC”
  * `Id` anges till domännamnet för datorn där det webbaserade programmet körs.
  * `OemName` är inställd på värdet som extraheras från den `Win32_ComputerSystem.Manufacturer` med hjälp av WMI.
  * `Model` är inställd på värdet som extraheras från den `Win32_ComputerSystem.Model` med hjälp av WMI.
  * `NetworkType` är inställd på värdet som extraheras från den `NetworkInterface`.
  * `Language` anges till namnet på den `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` uppdateringar i `RoleInstance` egenskapen för den `Device` kontext för alla telemetri objekt med domännamn på den dator där det webbaserade programmet körs.
* `OperationNameTelemetryInitializer` uppdateringar i `Name` egenskapen för den `RequestTelemetry` och `Name` egenskapen för den `Operation` kontext för alla objekt som telemetri baserat på HTTP-metoden, samt namnen på ASP.NET MVC-kontrollanten och åtgärden som anropas för att bearbeta begäran.
* `OperationIdTelemetryInitializer` eller `OperationCorrelationTelemetryInitializer` uppdateringar i `Operation.Id` kontextegenskap för alla objekt som telemetri spåras när en begäran med den automatiskt genererade `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` uppdateringar i `Id` egenskapen för den `Session` kontext för alla telemetri artiklar med extraheras från den `ai_session` cookie som genererats av ApplicationInsights JavaScript instrumentation koden som körs i användarens webbläsare.
* `SyntheticTelemetryInitializer` eller `SyntheticUserAgentTelemetryInitializer` uppdateringar i `User`, `Session`, och `Operation` kontexter egenskaper för alla objekt som telemetri spåras vid hantering av en begäran från en syntetisk källa, t.ex. en tillgänglighetsuppsättning testa eller söker motorn bot. Som standard [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) visas inte syntetiska telemetri.

    Den `<Filters>` ange identifierar egenskaperna för begäranden.
* `UserTelemetryInitializer` uppdateringar i `Id` och `AcquisitionDate` egenskaperna för `User` kontext för alla telemetri-objekt med värden som extraheras från den `ai_user` cookie som genererats av Application Insights JavaScript instrumentation koden som körs i användarens webbläsaren.
* `WebTestTelemetryInitializer` Anger användar-id, sessions-id och syntetiska Källegenskaper för HTTP-förfrågningar som kommer från [tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md).
  Den `<Filters>` ange identifierar egenskaperna för begäranden.

För .NET-program som körs i Service Fabric, kan du inkludera den `Microsoft.ApplicationInsights.ServiceFabric` NuGet-paketet. Det här paketet innehåller en `FabricTelemetryInitializer`, som lägger till Service Fabric-egenskaper till telemetrin objekt. Mer information finns i den [GitHub-sidan](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) om egenskaper som har lagts till av den här NuGet-paketet.

## <a name="telemetry-processors-aspnet"></a>Telemetri processorer (ASP.NET)
Telemetri processorer kan filtrera och ändra varje telemetriobjekt precis innan det skickas från SDK: N till portalen.

Du kan [skriva din egen telemetri processorer](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Adaptiv sampling telemetri processor (från 2.0.0-beta3)
Den här funktionen är aktiverad som standard. Om din app skickar stora mängder telemetri, den här processor tar bort några av den.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametern innehåller mål som algoritmen som försöker nå. Varje instans av SDK: N fungerar oberoende av varandra, så om din server är ett kluster med flera datorer, den faktiska mängden telemetri ska multipliceras därefter.

[Läs mer om sampling](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Fast räntesats sampling telemetri processor (från 2.0.0-beta1)
Det finns också en standard [samlar telemetri processor](../../azure-monitor/app/api-filtering-sampling.md) (från 2.0.1):

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
Dessa parametrar påverkar hur Java SDK ska lagra och tömma den samlar in telemetridata.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Antal telemetri-objekt som kan lagras i SDK: er minnesintern lagring. När antalet har uppnåtts, de telemetri skickas – det vill säga objekten telemetri skickas till Application Insights-server.

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
Anger hur ofta de data som lagras i InMemory-lagring ska tas bort (skickade till Application Insights).

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
Anger den maximala storleken i MB som tilldelas till beständig lagring på den lokala disken. Den här lagringen används för bestående telemetri-objekt som inte kunde överföras till Application Insights-slutpunkten. När storlek har uppnåtts, ignoreras nya telemetri objekt.

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

#### <a name="local-forwarder"></a>Lokal vidarebefordrare

[Lokala vidarebefordrare](https://docs.microsoft.com/azure/application-insights/opencensus-local-forwarder) är en agent som samlar in Application Insights eller [OpenCensus](https://opencensus.io/) telemetri från en mängd olika SDK: er och ramverk och den vidare till Application Insights. Det kan köras under Windows och Linux. När de kombineras med Application Insights Java SDK lokala vidarebefordraren ger fullständigt stöd för [Live Metrics](../../azure-monitor/app/live-stream.md) och Adaptiv sampling.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Om du använder SpringBoot starter, lägger du till följande konfigurationsfilen (application.properties):

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Standardvärden är desamma för SpringBoot application.properties och applicationinsights.xml konfiguration.

## <a name="instrumentationkey"></a>InstrumentationKey
Detta avgör den Application Insights-resurs där dina data visas. Vanligtvis skapar du en separat resurs med en separat nyckel för var och en av dina program.

Om du vill ange nyckeln dynamiskt – till exempel om du vill skicka resultaten från ditt program till olika resurser – kan du utelämna nyckeln från konfigurationsfilen och ange den i koden i stället.

För att ställa in nyckeln för alla instanser av TelemetryClient, ange inklusive standardmoduler moduler, nyckeln i TelemetryConfiguration.Active. Du kan göra detta i en initieringsmetod, till exempel global.aspx.cs i en ASP.NET-tjänst:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

Om du vill skicka en specifik uppsättning händelser till en annan resurs anger du nyckeln för en specifik TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Att hämta en ny nyckel [skapa en ny resurs i Application Insights-portalen][new].



## <a name="applicationid-provider"></a>ApplicationId Provider

_Från och med v2.6.0_

Syftet med den här providern är att söka efter ett program-ID som är baserat på en Instrumenteringsnyckel. Program-ID i RequestTelemetry och DependencyTelemetry och används för att fastställa korrelation i portalen.

Detta är tillgängligt genom att ange `TelemetryConfiguration.ApplicationIdProvider` antingen i kod eller i konfigurationen.

### <a name="interface-iapplicationidprovider"></a>Gränssnitt: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Vi tillhandahåller två implementeringar i den [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: `ApplicationInsightsApplicationIdProvider` och `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Det här är en omslutning runt vårt API för profilen. Det kommer begränsning begäranden och cache resultat.

Den här providern läggs till i config-filen när du installerar antingen [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) eller [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Den här klassen har en valfri egenskap `ProfileQueryEndpoint`.
Detta är som standard `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Om du behöver konfigurera en proxyserver för den här konfigurationen, rekommenderar vi att proxy är grundläggande adress och inklusive ”/api/profiler/{0}/appId”. Observera att '{0}' ersätts vid körning per begäran med Instrumenteringsnyckeln.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exempel på konfiguration via ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Exempel på konfiguration via kod:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Det här är en statisk provider som förlitar sig på konfigurerade Instrumenteringsnyckeln / program-ID-par.

Den här klassen har en egenskap `Defined`, vilket är en Dictionary < string, string > med Instrumenteringsnyckeln till program-ID-par.

Den här klassen har en valfri egenskap `Next` som kan användas för att konfigurera en annan provider som ska användas när en Instrumenteringsnyckel begärs som inte finns i din konfiguration.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exempel på konfiguration via ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Exempel på konfiguration via kod:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Nästa steg
[Mer information om API: et][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
