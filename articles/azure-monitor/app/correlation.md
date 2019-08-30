---
title: Korrelation för Azure Application Insights-telemetri | Microsoft Docs
description: Korrelation för Application Insights telemetri
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: bb28171ceca9861fb5cc0b7be1db9ab58ef72a1b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124105"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetri korrelation i Application Insights

I världen för mikrotjänster kräver varje logisk åtgärd att arbete utförs i olika komponenter i tjänsten. Var och en av dessa komponenter kan övervakas separat genom att [Azure Application](../../azure-monitor/app/app-insights-overview.md)insikter. Komponenten Web-App kommunicerar med komponenten autentiseringsprovider för att verifiera användarautentiseringsuppgifter och med API-komponenten för att hämta data för visualisering. API-komponenten kan fråga efter data från andra tjänster och använda cache-Provider-komponenter för att meddela fakturerings komponenten om det här anropet. Application Insights stöder korrelation med distribuerad telemetri, som du använder för att identifiera vilken komponent som ansvarar för misslyckanden eller prestanda försämring.

I den här artikeln förklaras data modellen som används av Application Insights för att korrelera telemetri som skickas av flera komponenter. Den täcker tekniker och protokoll för kontext spridning. Det omfattar också implementeringen av korrelations begrepp för olika språk och plattformar.

## <a name="data-model-for-telemetry-correlation"></a>Data modell för telemetri-korrelation

Application Insights definierar en [data modell](../../azure-monitor/app/data-model.md) för korrelation med distribuerad telemetri. För att associera telemetri med den logiska åtgärden, har varje telemetri-objekt ett kontext `operation_Id`fält som kallas. Den här identifieraren delas av varje telemetri-objekt i den distribuerade spårningen. Till och med förlust av telemetri från ett enda lager, kan du fortfarande associera telemetri som rapporteras av andra komponenter.

En distribuerad logisk åtgärd består vanligt vis av en uppsättning mindre åtgärder, som är begär Anden som bearbetas av en av komponenterna. De här åtgärderna definieras av [telemetri för begäran](../../azure-monitor/app/data-model-request-telemetry.md). Varje begäran om telemetri har en `id` egen som identifierar den unikt och globalt. Och alla telemetri-objekt (till exempel spår och undantag) som är associerade med den här begäran ska `operation_parentId` ange värdet för begäran. `id`

Varje utgående åtgärd, till exempel ett HTTP-anrop till en annan komponent, representeras av [beroende telemetri](../../azure-monitor/app/data-model-dependency-telemetry.md). Beroende telemetri definierar också den egna `id` som är globalt unik. Telemetri för begäran, som initieras av det här beroende anropet, `operation_parentId`använder detta `id` som dess.

Du kan bygga en vy av den distribuerade logiska åtgärden genom `operation_Id`att `operation_parentId`använda, `request.id` , `dependency.id`och med. Dessa fält definierar också orsakssambandet för telemetri samtal.

I en miljö med mikrotjänster kan spår från komponenter gå till olika lagrings objekt. Varje komponent kan ha sin egen Instrumentation-nyckel i Application Insights. För att få telemetri för den logiska åtgärden frågar Application Insights-UX data från varje lagrings objekt. När antalet lagrings objekt är enorma, behöver du ett tips om var du kan se nästa. Application Insights data modellen definierar två fält för att lösa det här problemet `request.source` : `dependency.target`och. Det första fältet identifierar komponenten som initierade beroende förfrågningen och den andra identifierar vilken komponent som returnerade svaret på beroende anropet.

## <a name="example"></a>Exempel

Vi tar ett exempel på ett program som kallas aktie kurser, som visar det aktuella marknads priset för en aktie med hjälp av ett externt API `Stock`som kallas. Programmet för aktie kurser har en sida med `Stock page` namnet att klientens webbläsare öppnas med hjälp `GET /Home/Stock`av. Programmet frågar API: `Stock` et genom att använda ett http `GET /api/stock/value`-anrop.

Du kan analysera den resulterande Telemetrin genom att köra en fråga:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Observera att alla telemetri-objekt delar roten `operation_Id`i resultatet. När ett AJAX-anrop görs från sidan, tilldelas ett nytt unikt ID (`qJSXU`) till beroende telemetri och ID: t för sid visningar används som. `operation_ParentId` Serverbegäran använder sedan Ajax-ID: t som `operation_ParentId`.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| Sid visningar   | Pappers sida                |              | STYz               | STYz         |
| beroende | Hämta/Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | Hämta hem/aktie            | KqKwlrSt9PA= | qJSXU              | STYz         |
| beroende | Hämta/API/Stock/Value      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

När anropet `GET /api/stock/value` görs till en extern tjänst vill du känna till identiteten för servern så att du kan ange fältet på `dependency.target` rätt sätt. När den externa tjänsten inte har stöd för `target` övervakning, anges värd namnet för tjänsten ( `stock-prices-api.com`till exempel). Men om tjänsten identifierar sig själv genom att returnera ett fördefinierat http `target` -huvud innehåller tjänst identiteten som gör att Application Insights kan bygga en distribuerad spårning genom att fråga telemetri från den tjänsten.

## <a name="correlation-headers"></a>Korrelations rubriker

Vi arbetar med ett RFC-förslag för [korrelations-HTTP-protokollet](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Det här förslaget definierar två huvuden:

- `Request-Id`: Har ett globalt unikt ID för anropet.
- `Correlation-Context`: Innehåller namn-värde-par samlingen för egenskaper för distribuerad spårning.

Standarden definierar också två scheman för `Request-Id` generation: flat och hierarkisk. Med det flata schemat definieras en välkänd `Id` nyckel `Correlation-Context` för samlingen.

Application Insights definierar [tillägget](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) för korrelations-http-protokollet. Den använder `Request-Context` namn/värde-par för att sprida den samling av egenskaper som används av den omedelbara anroparen eller anrops mottagaren. I Application Insights SDK används den här rubriken för `dependency.target` att `request.source` ställa in och fält.

### <a name="w3c-distributed-tracing"></a>Distribuerad W3C-spårning

Vi går över till [W3C Distributed tracing-format](https://w3c.github.io/trace-context/). Den definierar:

- `traceparent`: Utför det globalt unika åtgärds-ID: t och unik identifierare för anropet.
- `tracestate`: Utför spårning av systemsäker kontext.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Aktivera stöd för distribuerad W3C-spårning för klassiska ASP.NET-appar

Den här funktionen är tillgänglig `Microsoft.ApplicationInsights.Web` i `Microsoft.ApplicationInsights.DependencyCollector` och paket som börjar med version 2.8.0-beta1.
Den är inaktive rad som standard. Ändra `ApplicationInsights.config`följande om du vill aktivera den:

- Under `RequestTrackingTelemetryModule` `true`lägger du till elementetmedvärdetinställtpå.`EnableW3CHeadersExtraction`
- Under `DependencyTrackingTelemetryModule` `true`lägger du till elementetmedvärdetinställtpå.`EnableW3CHeadersInjection`
- Lägg `W3COperationCorrelationTelemetryInitializer` till under `TelemetryInitializers` det som liknar 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Aktivera stöd för distribuerad W3C-spårning för ASP.NET Core appar

Den här funktionen finns `Microsoft.ApplicationInsights.AspNetCore` i version 2.5.0-beta1 och `Microsoft.ApplicationInsights.DependencyCollector` i version 2.8.0-beta1.
Den är inaktive rad som standard. Om du vill aktivera det `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` anger `true`du till:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Aktivera stöd för distribuerad W3C-spårning för Java-appar

- **Inkommande konfiguration**

  - För Java EE-appar lägger du till följande i `<TelemetryModules>` taggen i ApplicationInsights. XML:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - För våren Boot-appar lägger du till följande egenskaper:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Utgående konfiguration**

  Lägg till följande i AI-Agent. XML:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Bakåtkompatibla kompatibilitetsläge är aktiverat som standard och `enableW3CBackCompat` parametern är valfri. Använd bara det när du vill inaktivera bakåtkompatibilitet.
  >
  > Vi rekommenderar att du inaktiverar det här när alla dina tjänster har uppdaterats till nyare versioner av SDK: er som stöder W3C-protokollet. Vi rekommenderar starkt att du flyttar till de nya SDK: erna så snart som möjligt.

> [!IMPORTANT]
> Se till att både inkommande och utgående konfigurationer är exakt desamma.

#### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Aktivera stöd för distribuerad W3C-spårning för webbappar

Den här funktionen finns `Microsoft.ApplicationInsights.JavaScript`i. Den är inaktive rad som standard. Använd `distributedTracingMode` config om du vill aktivera det. AI_AND_W3C tillhandahålls för bakåtkompatibilitet med alla äldre Application Insights instrumenterade tjänster:

- **NPM-installation (ignorera om du använder installations programmet för kodfragment)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Kodfragment-installation (ignorera om du använder NPM-installation)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...Other Configuration Options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>Opentracing och Application Insights

[Specifikationen](https://opentracing.io/) för opentracing-datamodellen och Application Insights data modeller mappas på följande sätt:

| Application Insights                  | Opentracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span`för`span.kind = server`                  |
| `Dependency`                          | `Span`för`span.kind = client`                  |
| `Id`av `Request` och`Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference`av typen `ChildOf` (det överordnade intervallet)   |

Mer information finns i [data modellen Application Insights telemetri](../../azure-monitor/app/data-model.md). 

Definitioner av opentracing-koncept finns i opentracing- [specifikation](https://github.com/opentracing/specification/blob/master/specification.md) och [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Telemetri-korrelation i .NET

Med tiden har .NET definierat flera sätt att korrelera telemetri-och diagnostikloggar:

- `System.Diagnostics.CorrelationManager`möjliggör spårning av [LogicalOperationStack och ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource`och ETW (Event Tracing for Windows) (ETW) definiera [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) -metoden.
- `ILogger`använder [logg omfattningar](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Kontext spridning för Windows Communication Foundation (WCF) och HTTP-kabelering.

Dessa metoder har dock inte aktiverat stöd för automatisk Distributed spårning. `DiagnosticSource`är ett sätt att stödja automatisk korrelation mellan datorer. .NET-bibliotek stöder "DiagnosticSource" och tillåter automatisk spridning mellan datorer av korrelations kontexten via transporten, till exempel HTTP.

[Guiden för aktiviteter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) i `DiagnosticSource` förklarar grunderna för spårning av aktiviteter.

ASP.NET Core 2,0 stöder extrahering av HTTP-huvuden och start av en ny aktivitet.

`System.Net.HttpClient`, från och med version 4.1.0, stöder automatisk inmatning av korrelations-HTTP-huvudena och spårning av HTTP-anropet som en aktivitet.

Det finns en ny HTTP-modul, [Microsoft. ASPNET. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), för klassisk ASP.net. Den här modulen implementerar telemetri-korrelationen med hjälp `DiagnosticSource`av. Den startar en aktivitet baserat på inkommande begärandehuvuden. Den korrelerar också telemetri från olika stadier av bearbetning av begär Anden, även för fall när varje steg i bearbetningen av Internet Information Services (IIS) körs i en annan hanterad tråd.

Application Insights SDK, från och med version 2.4.0-beta1, använder `DiagnosticSource` och `Activity` för att samla in telemetri och associera den med den aktuella aktiviteten.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetri korrelation i Java SDK

[Application Insights SDK för Java](../../azure-monitor/app/java-get-started.md) stöder automatisk korrelation av telemetri som börjar med version 2.0.0. Den fylls `operation_id` i automatiskt för all telemetri (t. ex. spår, undantag och anpassade händelser) som utfärdats inom omfånget för en begäran. Det tar också hand om att sprida korrelations rubrikerna (beskrivs ovan) för tjänst-till-tjänst-anrop via HTTP, om [Java SDK](../../azure-monitor/app/java-agent.md) -agenten har kon figurer ATS.

> [!NOTE]
> Endast anrop som görs via Apache HTTPClient stöds för korrelations funktionen. Om du använder våren RestTemplate eller Feign kan båda användas med Apache HTTPClient under huven.

För närvarande stöds inte automatisk kontext spridning mellan meddelande tekniker (t. ex. Kafka, RabbitMQ eller Azure Service Bus). Det är dock möjligt att koda sådana scenarier manuellt med hjälp `trackDependency` av API: erna och. `trackRequest` I dessa API: er representerar en beroende telemetri ett meddelande som ställs av en tillverkare och begäran representerar ett meddelande som bearbetas av en konsument. I det här fallet måste `operation_id` både `operation_parentId` och vara spridda i meddelandets egenskaper.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Telemetri korrelation i asynkron Java-program

Om du vill korrelera telemetri i ett asynkront våren Boot-program följer du [den här](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) detaljerade artikeln. Den ger vägledning för att instrumentera vårens [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) och [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Rollnamn

Ibland kanske du vill anpassa hur komponent namn visas i [program kartan](../../azure-monitor/app/app-map.md). För att göra det kan du manuellt ange `cloud_RoleName` genom att göra något av följande:

- Om du använder våren boot med Application Insights våren Boot starter, är den enda nödvändiga ändringen att ange ditt anpassade namn för programmet i filen Application. Properties.

  `spring.application.name=<name-of-app>`

  Start programmet för fjäder start tilldelar `cloudRoleName` automatiskt det värde som du anger `spring.application.name` för egenskapen.

- Om du använder `WebRequestTrackingFilter` `WebAppNameContextInitializer` , anger program namnet automatiskt. Lägg till följande i konfigurations filen (ApplicationInsights. xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Om du använder moln kontext klassen:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Nästa steg

- Skriv [anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md).
- Avancerade korrelations scenarier i ASP.NET Core och ASP.NET finns i artikeln [spåra anpassade åtgärder](custom-operations-tracking.md) .
- Lär dig mer om att [ställa in cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) för andra SDK: er.
- Publicera alla komponenter i mikrotjänsten på Application Insights. Kolla in de [plattformar som stöds](../../azure-monitor/app/platforms.md).
- Se [data modellen](../../azure-monitor/app/data-model.md) för Application Insights typer.
- Lär dig hur du [utökar och filtrerar telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Granska [Application Insights config](configuration-with-applicationinsights-config.md)-referensen.
