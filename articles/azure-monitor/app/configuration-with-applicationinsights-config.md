---
title: ApplicationInsights.config referens - Azure | Microsoft-dokument
description: Aktivera eller inaktivera datainsamlingsmoduler och lägga till prestandaräknare och andra parametrar.
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: 460bd79e3a37c492301f7438112fef4487618fed
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982097"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurera Application Insights SDK:n med ApplicationInsights.config eller .xml
Application Insights .NET SDK består av ett antal NuGet-paket. [Kärnpaketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights) tillhandahåller API:et för att skicka telemetri till Application Insights. [Ytterligare paket](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) innehåller telemetrimoduler och *initialiserare* för automatisk spårning av telemetri från ditt program och dess kontext. *modules* Genom att justera konfigurationsfilen kan du aktivera eller inaktivera telemetrimoduler och initializers och ange parametrar för vissa av dem.

Konfigurationsfilen `ApplicationInsights.config` namnges `ApplicationInsights.xml`eller , beroende på typen av program. Det läggs automatiskt till i projektet när du [installerar de flesta versioner av SDK][start]. När du använder den automatiska upplevelsen från Visual Studio-mallprojekt som stöder **Lägg till > Application Insights Telemetri skapas**filen ApplicationInsights.config i projektets rotmapp och när den följs kopieras till lagerplatsmappen. Den läggs också till i en webbapp av [Statusövervakaren på en IIS-server][redfield]. Konfigurationsfilen ignoreras om [tillägg för Azure-webbplats](azure-web-apps.md) eller [tillägg för Azure VM och skalningsuppsättning för virtuella datorer](azure-vm-vmss-apps.md) används.

Det finns inte en motsvarande fil för att styra [SDK på en webbsida][client].

I det här dokumentet beskrivs de avsnitt som visas i konfigurationsfilen, hur de styr komponenterna i SDK och vilka NuGet-paket som läser in dessa komponenter.

> [!NOTE]
> ApplicationInsights.config- och .xml-instruktioner gäller inte för .NET Core SDK. Följ den [här](../../azure-monitor/app/asp-net-core.md) guiden om du konfigurerar .NET Core-program.

## <a name="telemetry-modules-aspnet"></a>Telemetrimoduler (ASP.NET)
Varje telemetrimodul samlar in en viss typ av data och använder kärn-API:et för att skicka data. Modulerna installeras av olika NuGet-paket, som också lägger till de rader som krävs i config-filen.

Det finns en nod i konfigurationsfilen för varje modul. Om du vill inaktivera en modul tar du bort noden eller kommenterar den.

### <a name="dependency-tracking"></a>Beroendespårning
[Beroendespårning](../../azure-monitor/app/asp-net-dependencies.md) samlar in telemetri om samtal som appen gör till databaser och externa tjänster och databaser. Om du vill att den här modulen ska fungera på en IIS-server måste du [installera Statusövervakaren][redfield].

Du kan också skriva din egen [beroendespårningskod med trackdependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Paketet Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet.

Beroenden kan samlas in automatiskt utan att ändra koden med hjälp av agentbaserad (kodlös) bifoga. Om du vill använda det i Azure-webbappar aktiverar du [tillägget Application Insights](azure-web-apps.md). Om du vill använda den i Azure VM eller Azure virtual machine scale set aktiverar [du tillägget Programövervakning för vm och skalningsuppsättning för virtuella datorer](azure-vm-vmss-apps.md).

### <a name="performance-collector"></a>Performance samlare
[Samlar in systemprestandaräknare](../../azure-monitor/app/performance-counters.md) som CPU, minne och nätverksbelastning från IIS-installationer. Du kan ange vilka räknare som ska samlas in, inklusive prestandaräknare som du har ställt in själv.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Paketet Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet.

### <a name="application-insights-diagnostics-telemetry"></a>Programinsikter Diagnostik Telemetri
Rapporterna `DiagnosticsTelemetryModule` fel i Application Insights instrumentering kod själv. Om koden till exempel inte kan komma `ITelemetryInitializer` åt prestandaräknare eller om en kastar ett undantag. Spåra telemetri som spåras av den här modulen visas i [diagnostiksökningen][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Utvecklarläge
`DeveloperModeWithDebuggerAttachedTelemetryModule`tvingar Application Insights `TelemetryChannel` att skicka data omedelbart, ett telemetriobjekt i taget, när en felsökare är kopplad till programprocessen. Detta minskar tiden mellan det ögonblick då programmet spårar telemetri och när det visas på application insights-portalen. Det orsakar betydande omkostnader i CPU och nätverksbandbredd.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Programinsikter Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet-paketet

### <a name="web-request-tracking"></a>Spårning av webbbegäran
Rapporterar [svarstid och resultatkod](../../azure-monitor/app/asp-net.md) för HTTP-begäranden.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Package Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet

### <a name="exception-tracking"></a>Spårning av undantag
`ExceptionTrackingTelemetryModule`spårar ohanterade undantag i webbappen. Se [Fel och undantag][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Package Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`- spårar [oobserverade uppgiftsund undantag](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`- spårar ohanterade undantag för arbetsroller, Windows-tjänster och konsolprogram.
* [Programinsikter Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet paket.

### <a name="eventsource-tracking"></a>Spårning av eventsource
`EventSourceTelemetryModule`kan du konfigurera EventSource-händelser som ska skickas till Application Insights som spårningar. Information om hur du spårar EventSource-händelser finns i [Använda EventSource-händelser](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW-händelsespårning
`EtwCollectorTelemetryModule`kan du konfigurera händelser från ETW-providers som ska skickas till Application Insights som spårningar. Information om hur du spårar ETW-händelser finns i [Använda ETW-händelser](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector Microsoft.ApplicationInsights.EtwCollector Microsoft.ApplicationInsights.EtwCollector Microsoft.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInights Microsoft.ApplicationInsights Microsoft.ApplicationInsights Microsoft.
Microsoft.ApplicationInsights-paketet tillhandahåller SDK:s [kärn-API.](https://msdn.microsoft.com/library/mt420197.aspx) De andra telemetrimodulerna använder detta och du kan också [använda den för att definiera din egen telemetri](../../azure-monitor/app/api-custom-events-metrics.md).

* Ingen post i ApplicationInsights.config.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet-paketet. Om du bara installerar den här NuGet genereras ingen config-fil.

## <a name="telemetry-channel"></a>Telemetrikanal
[Telemetrikanalen](telemetry-channels.md) hanterar buffring och överföring av telemetri till application insights-tjänsten.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`är standardkanalen för webbprogram. Den buffrar data i minnet och använder återförsöksmekanismer och lokal disklagring för mer tillförlitlig telemetrileverans.
* `Microsoft.ApplicationInsights.InMemoryChannel`är en lätt telemetrikanal som används om ingen annan kanal är konfigurerad. 

## <a name="telemetry-initializers-aspnet"></a>Telemetriinitierare (ASP.NET)
Telemetriinitierare anger kontextegenskaper som skickas tillsammans med varje post i telemetri.

Du kan [skriva egna initializers](../../azure-monitor/app/api-filtering-sampling.md#add-properties) för att ange kontextegenskaper.

Standardinitierarna är alla inställda antingen av web- eller WindowsServer NuGet-paketen:

* `AccountIdTelemetryInitializer`anger egenskapen AccountId.
* `AuthenticatedUserIdTelemetryInitializer`anger egenskapen AuthenticatedUserId enligt JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer`uppdaterar `RoleName` `RoleInstance` kontextens `Device` egenskaper för alla telemetriobjekt med information som extraherats från Azure-körningsmiljön.
* `BuildInfoConfigComponentVersionTelemetryInitializer`uppdaterar `Version` kontextens `Component` egenskap för alla telemetriobjekt med `BuildInfo.config` det värde som extraherats från filen som produceras av MS Build.
* `ClientIpHeaderTelemetryInitializer`uppdaterar `Ip` egenskapen `Location` för kontexten för alla `X-Forwarded-For` telemetriobjekt baserat på HTTP-huvudet för begäran.
* `DeviceTelemetryInitializer`uppdaterar följande egenskaper `Device` för kontexten för alla telemetriobjekt.
  * `Type`är inställd på "PC"
  * `Id`är inställt på domännamnet för den dator där webbprogrammet körs.
  * `OemName`är inställt på det värde `Win32_ComputerSystem.Manufacturer` som extraheras från fältet med WMI.
  * `Model`är inställt på det värde `Win32_ComputerSystem.Model` som extraheras från fältet med WMI.
  * `NetworkType`är inställt på det värde `NetworkInterface`som extraheras från .
  * `Language`är inställt på namnet `CurrentCulture`på .
* `DomainNameRoleInstanceTelemetryInitializer`uppdaterar `RoleInstance` egenskapen `Device` för kontexten för alla telemetriobjekt med domännamnet för den dator där webbprogrammet körs.
* `OperationNameTelemetryInitializer`uppdaterar `Name` egenskapen `RequestTelemetry` för `Name` och egenskapen för kontexten `Operation` för alla telemetriobjekt baserat på HTTP-metoden, samt namn på ASP.NET MVC-styrenhet och åtgärd som anropas för att bearbeta begäran.
* `OperationIdTelemetryInitializer`eller `OperationCorrelationTelemetryInitializer` uppdaterar `Operation.Id` kontextegenskapen för alla telemetriobjekt som spåras när en begäran hanteras med den automatiskt genererade `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`uppdaterar `Id` kontextens `Session` egenskap för alla telemetriobjekt med `ai_session` värde som extraherats från cookien som genereras av ApplicationInsights JavaScript-instrumenteringskoden som körs i användarens webbläsare.
* `SyntheticTelemetryInitializer`eller `SyntheticUserAgentTelemetryInitializer` uppdaterar `User` `Session`egenskaperna `Operation` för alla telemetriobjekt som spåras när en begäran hanteras från en syntetisk källa, till exempel ett tillgänglighetstest eller sökrobot. Som standard visar [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) inte syntetisk telemetri.

    De `<Filters>` ange identifieringsegenskaperna för begäranden.
* `UserTelemetryInitializer`uppdaterar `Id` `AcquisitionDate` `User` kontextens egenskaper för alla telemetriobjekt med `ai_user` värden som extraherats från cookien som genereras av Application Insights JavaScript-instrumenteringskoden som körs i användarens webbläsare.
* `WebTestTelemetryInitializer`anger användar-ID, sessions-ID och syntetiska källegenskaper för HTTP-begäranden som kommer från [tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md).
  De `<Filters>` ange identifieringsegenskaperna för begäranden.

För .NET-program som körs i `Microsoft.ApplicationInsights.ServiceFabric` Service Fabric kan du inkludera NuGet-paketet. Det här `FabricTelemetryInitializer`paketet innehåller en , som lägger till Service Fabric-egenskaper i telemetriartiklar. Mer information finns på [GitHub-sidan](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) om de egenskaper som lagts till i det här NuGet-paketet.

## <a name="telemetry-processors-aspnet"></a>Telemetriprocessorer (ASP.NET)
Telemetriprocessorer kan filtrera och ändra varje telemetriobjekt precis innan det skickas från SDK till portalen.

Du kan [skriva egna telemetriprocessorer](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Adaptiv provtagning Telemetriprocessor (från 2.0.0-beta3)
Den här funktionen är aktiverad som standard. Om din app skickar en hel del telemetri, tar den här processorn bort en del av den.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametern ger det mål som algoritmen försöker uppnå. Varje instans av SDK fungerar oberoende av varandra, så om servern är ett kluster av flera datorer multipliceras den faktiska volymen av telemetri i enlighet med detta.

[Läs mer om sampling](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Provtagnings-processor med fast hastighet (från 2.0.0-beta1)
Det finns också en [standardprovtagning telemetriprocessor](../../azure-monitor/app/api-filtering-sampling.md) (från 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>Instrumenteringsnyckel
Detta avgör den application insights-resurs där dina data visas. Vanligtvis skapar du en separat resurs, med en separat nyckel, för vart och ett av dina program.

Om du vill ställa in nyckeln dynamiskt , till exempel om du vill skicka resultat från ditt program till olika resurser - kan du utelämna nyckeln från konfigurationsfilen och ställa in den i kod i stället.

Så här anger du nyckeln för alla instanser av TelemetryClient, inklusive standardmoduler för telemetri. Gör detta i en initieringsmetod, till exempel global.aspx.cs i en ASP.NET tjänst:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Om du bara vill skicka en viss uppsättning händelser till en annan resurs kan du ange nyckeln för en viss TelemetriClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Skapa en ny [resurs i portalen Application Insights][new]om du vill hämta en ny nyckel .



## <a name="applicationid-provider"></a>ApplicationId-provider

_Tillgänglig från och med v2.6.0_

Syftet med den här providern är att söka efter ett program-ID baserat på en instrumenteringsnyckel. Program-ID ingår i RequestTelemetry och DependencyTelemetry och används för att fastställa korrelation i portalen.

Detta är tillgängligt `TelemetryConfiguration.ApplicationIdProvider` genom att ange antingen i kod eller i config.

### <a name="interface-iapplicationidprovider"></a>Gränssnitt: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Vi tillhandahåller två implementeringar i [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) `ApplicationInsightsApplicationIdProvider` `DictionaryApplicationIdProvider`sdk: och .

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Detta är ett omslag runt vårt profil-API. Det kommer att begränsa begäranden och cacheresultat.

Den här providern läggs till i konfigurationsfilen när du installerar antingen [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) eller [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Den här klassen `ProfileQueryEndpoint`har en valfri egenskap .
Som standard är `https://dc.services.visualstudio.com/api/profiles/{0}/appId`detta inställt på .
Om du behöver konfigurera en proxy för den här konfigurationen rekommenderar vi att{0}du proxyerar basadressen och inkluderar "/api/profiles/ /appId". Observera att{0}' ' ersätts vid körning per begäran med instrumenteringsnyckeln.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exempel konfiguration via ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Exempel konfiguration via kod:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Detta är en statisk leverantör, som kommer att förlita sig på din konfigurerade Instrumentation Key / Application ID par.

Den här klassen `Defined`har en egenskap , som är en ordlista<sträng,sträng> instrumenteringsnyckel till program-ID-par.

Den här klassen `Next` har en valfri egenskap som kan användas för att konfigurera en annan provider att använda när en instrumenteringsnyckel begärs som inte finns i konfigurationen.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exempel konfiguration via ApplicationInsights.config:
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

#### <a name="example-configuration-via-code"></a>Exempel konfiguration via kod:
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
[Läs mer om API][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
