---
title: ApplicationInsights. config-referens – Azure | Microsoft Docs
description: Aktivera eller inaktivera moduler för data insamling och Lägg till prestanda räknare och andra parametrar.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 382f43156ab450600ff0d2e5e2db763cd6bd94df
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875056"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurera Application Insights SDK:n med ApplicationInsights.config eller .xml
Application Insights .NET SDK består av ett antal NuGet-paket. [Kärn paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights) innehåller API: et för att skicka telemetri till Application Insights. [Ytterligare paket](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) tillhandahåller telemetri- *moduler* och *initierare* för automatisk spårning av telemetri från ditt program och dess kontext. Genom att justera konfigurations filen kan du aktivera eller inaktivera moduler för telemetri och initierare och ange parametrar för några av dem.

Konfigurations filen heter `ApplicationInsights.config` eller `ApplicationInsights.xml`, beroende på typen av program. Den läggs automatiskt till i projektet när du [installerar de flesta versioner av SDK][start]. Den läggs också till i en webbapp genom att [statusövervakare på en IIS-server][redfield]. Konfigurations filen ignoreras om [tillägget för Azure-webbplatsen](azure-web-apps.md) eller [tillägget för Azure VM och skalnings uppsättningen för virtuella datorer](azure-vm-vmss-apps.md) används.

Det finns ingen motsvarande fil för att kontrol lera [SDK: n på en webb sida][client].

Det här dokumentet beskriver de avsnitt som visas i konfigurations filen, hur de styr komponenterna i SDK och vilka NuGet-paket som läser in dessa komponenter.

> [!NOTE]
> ApplicationInsights. config och. XML-instruktioner gäller inte för .NET Core SDK. Följ [den här](../../azure-monitor/app/asp-net-core.md) guiden för att konfigurera .net Core-program.

## <a name="telemetry-modules-aspnet"></a>Telemetri-moduler (ASP.NET)
Varje telemetri-modul samlar in en speciell typ av data och använder huvud-API: et för att skicka data. Modulerna installeras av olika NuGet-paket, vilket även lägger till de rader som krävs i. config-filen.

Det finns en nod i konfigurations filen för varje modul. Om du vill inaktivera en modul tar du bort noden eller kommenterar den.

### <a name="dependency-tracking"></a>Beroende spårning
[Beroende spårning](../../azure-monitor/app/asp-net-dependencies.md) samlar in telemetri om samtal som din app gör till databaser och externa tjänster och databaser. Om du vill tillåta att den här modulen fungerar på en IIS-server måste du [installera statusövervakare][redfield].

Du kan också skriva en egen beroende spårnings kod med [TrackDependency-API: et](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet-paketet.

Beroenden kan samlas in automatiskt utan att ändra din kod med hjälp av grupprincipbaserad (kodad) anslutning. Aktivera [Application Insights-tillägget](azure-web-apps.md)om du vill använda det i Azure-webbappar. Om du vill använda det i virtuell Azure-dator eller skalnings uppsättning för virtuella Azure-datorer aktiverar du [program övervaknings tillägget för virtuell dator och skal uppsättning för virtuell dator](azure-vm-vmss-apps.md).

### <a name="performance-collector"></a>Prestanda insamlare
[Samlar in system prestanda räknare](../../azure-monitor/app/performance-counters.md) som CPU, minne och nätverks belastning från IIS-installationer. Du kan ange vilka räknare som ska samlas in, inklusive prestanda räknare som du själv har konfigurerat.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet-paketet.

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights Diagnostics-telemetri
`DiagnosticsTelemetryModule` Rapporterar fel i själva Application Insights Instrumentation-koden. Om koden till exempel inte kan komma åt prestanda räknare eller om en `ITelemetryInitializer` genererar ett undantag. Trace-telemetri som spåras av den här modulen visas i den [diagnostiska sökningen][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Utvecklarläge
`DeveloperModeWithDebuggerAttachedTelemetryModule`tvingar Application Insights `TelemetryChannel` att skicka data direkt, ett telemetridata i taget, när en fel sökare är kopplad till program processen. Detta minskar tiden mellan tidpunkten då programmet spårar telemetri och när det visas på Application Insights-portalen. Det orsakar betydande kostnader för processor-och nätverks bandbredd.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-paket

### <a name="web-request-tracking"></a>Spårning av webb förfrågningar
Rapporterar [svars tid och resultat kod](../../azure-monitor/app/asp-net.md) för HTTP-begäranden.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. Web NuGet-](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paket

### <a name="exception-tracking"></a>Undantags spårning
`ExceptionTrackingTelemetryModule`spårar ohanterade undantag i din webbapp. Se [fel och undantag][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft. ApplicationInsights. Web NuGet-](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paket
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`– spårar ej [observerade uppgifts undantag](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`– spårar ohanterade undantag för arbets roller, Windows-tjänster och konsol program.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-paket.

### <a name="eventsource-tracking"></a>EventSource spårning
`EventSourceTelemetryModule`gör att du kan konfigurera EventSource-händelser så att de skickas till Application Insights som spår. Information om hur du spårar EventSource-händelser finns i [använda EventSource-händelser](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Spårning av ETW-händelser
`EtwCollectorTelemetryModule`gör att du kan konfigurera händelser från ETW-leverantörer som ska skickas till Application Insights som spår. Information om hur du spårar ETW-händelser finns i [använda ETW-händelser](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Microsoft. ApplicationInsights-paketet innehåller [kärn-API: t](https://msdn.microsoft.com/library/mt420197.aspx) för SDK: n. De andra modulerna för telemetri använder detta, och du kan också [använda det för att definiera din egen telemetri](../../azure-monitor/app/api-custom-events-metrics.md).

* Ingen post i ApplicationInsights. config.
* [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-paket. Om du bara installerar den här NuGet genereras ingen. config-fil.

## <a name="telemetry-channel"></a>Telemetri kanal
[Telemetri-kanalen](telemetry-channels.md) hanterar buffring och överföring av telemetri till Application Insights tjänsten.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`är standard kanalen för webb program. Den buffrar data i minnet och använder mekanismer för nya försök och lokal disk lagring för mer Reliable telemetri-leverans.
* `Microsoft.ApplicationInsights.InMemoryChannel`är en Lightweight telemetri-kanal som används om ingen annan kanal konfigureras. 

## <a name="telemetry-initializers-aspnet"></a>Telemetri-initierare (ASP.NET)
Telemetri initierare anger kontext egenskaper som skickas tillsammans med varje objekt i telemetri.

Du kan [skriva egna initierare](../../azure-monitor/app/api-filtering-sampling.md#add-properties) för att ange kontext egenskaper.

Standard initierarna anges antingen av webb-eller Windows Server NuGet-paket:

* `AccountIdTelemetryInitializer`anger egenskapen AccountId.
* `AuthenticatedUserIdTelemetryInitializer`anger egenskapen AuthenticatedUserId som anges av JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer``RoleName` uppdaterar och `RoleInstance` egenskaper för`Device` kontexten för alla telemetri-objekt med information som extraheras från Azure runtime-miljön.
* `BuildInfoConfigComponentVersionTelemetryInitializer`uppdaterar egenskapen för kontexten för alla telemetri-objekt med det värde som extraheras `BuildInfo.config` från den fil som genererats av MS build. `Component` `Version`
* `ClientIpHeaderTelemetryInitializer`Egenskapen `Ip` updates `Location` för kontexten för alla `X-Forwarded-For` telemetri-objekt baserat på http-rubriken för begäran.
* `DeviceTelemetryInitializer`uppdaterar följande egenskaper `Device` för kontexten för alla telemetri-objekt.
  * `Type`har angetts till "PC"
  * `Id`har angetts till domän namnet för den dator där webb programmet körs.
  * `OemName`har värdet extraherat från `Win32_ComputerSystem.Manufacturer` fältet med WMI.
  * `Model`har värdet extraherat från `Win32_ComputerSystem.Model` fältet med WMI.
  * `NetworkType`har angetts till värdet som extraheras från `NetworkInterface`.
  * `Language`har angetts till namnet på `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer``RoleInstance` uppdaterar egenskapen `Device` för kontexten för alla telemetri-objekt med domän namnet för den dator där webb programmet körs.
* `OperationNameTelemetryInitializer``Name` uppdaterar egenskapen `RequestTelemetry` för och`Name`egenskapen för kontextenförallatelemetri-objektbaseratpåhttp-metoden,samtnamnpåASP.NETMVC-styrenhetochåtgärdsomanropasför`Operation` att bearbeta begäran.
* `OperationIdTelemetryInitializer`eller `OperationCorrelationTelemetryInitializer` `RequestTelemetry.Id`uppdaterar kontext egenskapen för alla telemetridata som spåras och hanterar en begäran med det automatiskt genererade objektet. `Operation.Id`
* `SessionTelemetryInitializer`uppdaterar egenskapen för kontexten för alla telemetri objekt `ai_session` med värdet som extraheras från cookien som genererats av ApplicationInsights JavaScript-Instrumentation-koden som körs i användarens webbläsare. `Session` `Id`
* `SyntheticTelemetryInitializer`eller `SyntheticUserAgentTelemetryInitializer` uppdateraregenskaperna`Session`,, och`Operation` för alla telemetridata som spåras vid hantering av en begäran från en syntetisk källa, till exempel ett tillgänglighets test eller en sökmotor bot. `User` Som standard visar [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) inte syntetisk telemetri.

    `<Filters>` Ange identifierings egenskaper för begär Anden.
* `UserTelemetryInitializer``Id` uppdaterar och `AcquisitionDate` egenskaper `ai_user` för `User` kontexten för alla telemetridata med värden som extraheras från cookien som genererats av Application Insights JavaScript-Instrumentation-koden som körs i användarens webbläsare.
* `WebTestTelemetryInitializer`anger egenskaperna för användar-ID, sessions-ID och syntetisk källa för HTTP-förfrågningar som kommer från [tillgänglighets test](../../azure-monitor/app/monitor-web-app-availability.md).
  `<Filters>` Ange identifierings egenskaper för begär Anden.

För .NET-program som körs i Service Fabric kan du inkludera `Microsoft.ApplicationInsights.ServiceFabric` NuGet-paketet. Det här paketet innehåller `FabricTelemetryInitializer`ett, som lägger till Service Fabric egenskaper till telemetri-objekt. Mer information finns på [sidan GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) om de egenskaper som lagts till av det här NuGet-paketet.

## <a name="telemetry-processors-aspnet"></a>Telemetri-processorer (ASP.NET)
Telemetri-processorer kan filtrera och ändra varje telemetri precis innan det skickas från SDK till portalen.

Du kan [skriva egna telemetri-processorer](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Anpassningsbar samplings process för telemetri (från 2.0.0-beta3)
Den här funktionen är aktiverad som standard. Om din app skickar mycket telemetri tar denna processor bort en del av den.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametern anger det mål som algoritmen försöker uppnå. Varje instans av SDK fungerar oberoende, så om servern är ett kluster med flera datorer multipliceras den faktiska mängden telemetri på motsvarande sätt.

[Lär dig mer om sampling](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processor med fast taxa för samplings frekvens (från 2.0.0-beta1)
Det finns också en standard [processor för sampling av telemetri](../../azure-monitor/app/api-filtering-sampling.md) (från 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Kanal parametrar (Java)
Dessa parametrar påverkar hur Java SDK ska lagra och rensa telemetridata som samlas in.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Antalet telemetri-objekt som kan lagras i SDK: s minnes lagring. När det här antalet uppnås, töms bufferten – det vill säga telemetri-objekten skickas till Application Insights-servern.

* Minimum 1
* Bekräftat 1000
* Objekt 500

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
Anger hur ofta de data som lagras i minnes lagringen ska tömmas (skickas till Application Insights).

* Minimum 1
* Bekräftat 300
* Objekt 5

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
Fastställer den maximala storlek i MB som tilldelas den beständiga lagringen på den lokala disken. Den här lagringen används för att bevara telemetri-objekt som inte kunde överföras till Application Insights slut punkten. När lagrings storleken är uppfyllt, kommer nya telemetridata att ignoreras.

* Minimum 1
* Bekräftat 100
* Objekt 10

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

[Lokal vidarebefordrare](opencensus-local-forwarder.md) är en agent som samlar in telemetri för Application Insights eller Open- [räkning](https://opencensus.io/) från en mängd SDK: er och ramverk och dirigerar dem till Application Insights. Den kan köras under Windows och Linux. När den är kopplad till Application Insights Java SDK ger den lokala vidarebefordraren fullständig support för [Live-mått](../../azure-monitor/app/live-stream.md) och anpassningsbar sampling.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Om du använder SpringBoot starter lägger du till följande i konfigurations filen (program. Properties):

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Standardvärden är desamma för SpringBoot-program. Properties-och applicationinsights. XML-konfiguration.

## <a name="instrumentationkey"></a>InstrumentationKey
Detta anger den Application Insights resurs som dina data visas i. Normalt skapar du en separat resurs med en separat nyckel för var och en av dina program.

Om du vill ange nyckeln dynamiskt, till exempel om du vill skicka resultat från ditt program till olika resurser, kan du utelämna nyckeln från konfigurations filen och ange den i kod i stället.

Om du vill ange nyckeln för alla instanser av TelemetryClient, inklusive standardmoduler för telemetri, anger du nyckeln i TelemetryConfiguration. Active. Gör detta i en initierings metod, till exempel global.aspx.cs i en ASP.NET-tjänst:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

Om du bara vill skicka en särskild uppsättning händelser till en annan resurs kan du ange nyckeln för en särskild TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Skapa en ny resurs i Application Insights portalen][new]för att få en ny nyckel.



## <a name="applicationid-provider"></a>ApplicationId-Provider

_Tillgänglig från och med v-2.6.0_

Syftet med den här providern är att söka efter ett program-ID baserat på en Instrumentation-nyckel. Program-ID: t ingår i RequestTelemetry och DependencyTelemetry och används för att fastställa korrelationen i portalen.

Detta är tillgängligt genom att `TelemetryConfiguration.ApplicationIdProvider` ställa in antingen i kod eller i konfig.

### <a name="interface-iapplicationidprovider"></a>Gränssnitt IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Vi tillhandahåller två implementeringar i SDK: n för [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) : `ApplicationInsightsApplicationIdProvider` och `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Detta är en omslutning runt vårt profil-API. Den kommer att begränsa begär Anden och cache-resultat.

Den här providern läggs till i konfigurations filen när du installerar antingen [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) eller [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Den här klassen har en valfri `ProfileQueryEndpoint`egenskap.
Som standard är detta inställt på `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Om du behöver konfigurera en proxyserver för den här konfigurationen rekommenderar vi att du proxyerar bas adressen och inkluderar "/API/Profiles/{0}/appId". Observera att "{0}" ersätts vid körning per begäran med Instrumentation-nyckeln.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exempel på konfiguration via ApplicationInsights. config:
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

Det här är en statisk provider som förlitar sig på den konfigurerade Instrumentation-nyckel/program-ID-paret.

Den här klassen har en `Defined`egenskap, som är en ord lista < sträng, String > för Instrumentation-nyckel till program-ID-par.

Den här klassen har en valfri `Next` egenskap som kan användas för att konfigurera en annan provider som ska användas när en Instrumentation-nyckel begärs som inte finns i konfigurationen.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exempel på konfiguration via ApplicationInsights. config:
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
[Läs mer om API: et][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
