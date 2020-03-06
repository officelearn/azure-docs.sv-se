---
title: Korrelation för Azure Application Insights-telemetri | Microsoft Docs
description: Korrelation för Application Insights telemetri
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367666"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetri korrelation i Application Insights

I världen för mikrotjänster kräver varje logisk åtgärd att arbete utförs i olika komponenter i tjänsten. Du kan övervaka var och en av dessa komponenter separat genom att använda [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights stöder korrelation med distribuerad telemetri, som du använder för att identifiera vilken komponent som ansvarar för misslyckanden eller prestanda försämring.

I den här artikeln förklaras data modellen som används av Application Insights för att korrelera telemetri som skickas av flera komponenter. Den täcker tekniker och protokoll för kontext spridning. Den omfattar också implementeringen av korrelations-taktiker på olika språk och plattformar.

## <a name="data-model-for-telemetry-correlation"></a>Data modell för telemetri-korrelation

Application Insights definierar en [data modell](../../azure-monitor/app/data-model.md) för korrelation med distribuerad telemetri. För att associera telemetri med en logisk åtgärd har varje telemetri-objekt ett kontext fält som kallas `operation_Id`. Den här identifieraren delas av varje telemetri-objekt i den distribuerade spårningen. Så även om du förlorar telemetri från ett enda lager, kan du fortfarande associera telemetri som rapporteras av andra komponenter.

En distribuerad logisk åtgärd består vanligt vis av en uppsättning mindre åtgärder som begär Anden bearbetas av en av komponenterna. De här åtgärderna definieras av [telemetri för begäran](../../azure-monitor/app/data-model-request-telemetry.md). Varje begär ande telemetri-objekt har en egen `id` som identifierar det unikt och globalt. Och alla telemetri-objekt (till exempel spår och undantag) som är associerade med begäran ska ange `operation_parentId` till värdet för begäran `id`.

Varje utgående åtgärd, till exempel ett HTTP-anrop till en annan komponent, representeras av [beroende telemetri](../../azure-monitor/app/data-model-dependency-telemetry.md). Beroende telemetri definierar också sin egen `id` som är globalt unik. Telemetri för begäran, som initieras av det här beroende anropet, använder den här `id` som `operation_parentId`.

Du kan bygga en vy av den distribuerade logiska åtgärden genom att använda `operation_Id`, `operation_parentId`och `request.id` med `dependency.id`. Dessa fält definierar också orsakssambandet för telemetri samtal.

I en miljö med mikrotjänster kan spår från komponenter gå till olika lagrings objekt. Varje komponent kan ha sin egen Instrumentation-nyckel i Application Insights. För att få telemetri för den logiska åtgärden, Application Insights fråga data från varje lagrings objekt. När antalet lagrings objekt är stort, behöver du ett tips om var du kan titta härnäst. Application Insights data modellen definierar två fält för att lösa det här problemet: `request.source` och `dependency.target`. Det första fältet identifierar komponenten som initierade beroende förfrågningen. Det andra fältet identifierar vilken komponent som returnerade svaret på beroende anropet.

## <a name="example"></a>Exempel

Nu ska vi titta på ett exempel. Ett program som kallas aktie kurser visar det aktuella marknads priset för en aktie genom att använda ett externt API som kallas lager. Programmet för aktie kurser har en sida som kallas lager sida som-klientens webbläsare öppnar med hjälp av `GET /Home/Stock`. Programmet skickar frågor till aktie-API: et genom att använda HTTP-anropet `GET /api/stock/value`.

Du kan analysera den resulterande Telemetrin genom att köra en fråga:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Observera i resultaten att alla telemetridata delar rot `operation_Id`. När ett AJAX-anrop görs från sidan, tilldelas ett nytt unikt ID (`qJSXU`) till beroende telemetri och ID: t för sid visningar används som `operation_ParentId`. Serverbegäran använder sedan Ajax-ID: t som `operation_ParentId`.

| ItemType   | namn                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| Sid visningar   | Pappers sida                |              | STYz               | STYz         |
| beroende | Hämta/Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | Hämta hem/aktie            | KqKwlrSt9PA= | qJSXU              | STYz         |
| beroende | Hämta/API/Stock/Value      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

När anrops `GET /api/stock/value` görs till en extern tjänst måste du känna till identiteten för servern så att du kan ställa in fältet `dependency.target` på lämpligt sätt. När den externa tjänsten inte har stöd för övervakning har `target` angetts som värd namnet för tjänsten (till exempel `stock-prices-api.com`). Men om tjänsten identifierar sig själv genom att returnera ett fördefinierat HTTP-huvud, `target` innehåller tjänst identiteten som gör att Application Insights kan bygga en distribuerad spårning genom att fråga telemetri från den tjänsten.

## <a name="correlation-headers"></a>Korrelations rubriker

Application Insights övergår till [W3C-spårnings kontext](https://w3c.github.io/trace-context/), som definierar:

- `traceparent`: utför det globalt unika åtgärds-ID: t och den unika identifieraren för anropet.
- `tracestate`: utför systemdefinierad spårnings kontext.

Den senaste versionen av Application Insights SDK stöder spårnings kontext protokollet, men du kan behöva välja att göra det. (Bakåtkompatibilitet med det tidigare korrelations protokoll som stöds av Application Insights SDK kommer att behållas.)

[Korrelations-http-protokollet, som även kallas Request-ID](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), är inaktuellt. Det här protokollet definierar två huvuden:

- `Request-Id`: bär ett globalt unikt ID för anropet.
- `Correlation-Context`: innehåller namn-värdepar-samlingen för de distribuerade spårnings egenskaperna.

Application Insights definierar också [tillägget](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) för korrelations-http-protokollet. Den använder `Request-Context` namn/värde-par för att sprida den samling egenskaper som används av den omedelbara anroparen eller anrops mottagaren. I Application Insights SDK används den här rubriken för att ange fälten `dependency.target` och `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Aktivera stöd för distribuerad W3C-spårning för klassiska ASP.NET-appar
 
  > [!NOTE]
  >  Från och med `Microsoft.ApplicationInsights.Web` och `Microsoft.ApplicationInsights.DependencyCollector`krävs ingen konfiguration.

Stöd för W3C-spårnings kontext implementeras på ett bakåtkompatibelt sätt. Korrelation förväntas fungera med program som är instrumenterade med tidigare versioner av SDK (utan stöd för W3C).

Om du vill fortsätta använda det äldre `Request-Id`-protokollet kan du inaktivera spårnings kontexten med hjälp av den här konfigurationen:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Om du kör en äldre version av SDK rekommenderar vi att du uppdaterar den eller tillämpar följande konfiguration för att aktivera spårnings kontext.
Den här funktionen är tillgänglig i `Microsoft.ApplicationInsights.Web` och `Microsoft.ApplicationInsights.DependencyCollector` paket, från och med version 2.8.0-beta1.
Den är inaktive rad som standard. Om du vill aktivera den gör du följande ändringar i `ApplicationInsights.config`:

- Under `RequestTrackingTelemetryModule`lägger du till `EnableW3CHeadersExtraction`-elementet och anger värdet till `true`.
- Under `DependencyTrackingTelemetryModule`lägger du till `EnableW3CHeadersInjection`-elementet och anger värdet till `true`.
- Lägg till `W3COperationCorrelationTelemetryInitializer` under `TelemetryInitializers`. Det ser ut ungefär som i det här exemplet:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Aktivera stöd för distribuerad W3C-spårning för ASP.NET Core appar

 > [!NOTE]
  > Från och med `Microsoft.ApplicationInsights.AspNetCore` version 2.8.0 krävs ingen konfiguration.
 
Stöd för W3C-spårnings kontext implementeras på ett bakåtkompatibelt sätt. Korrelation förväntas fungera med program som är instrumenterade med tidigare versioner av SDK (utan stöd för W3C).

Om du vill fortsätta använda det äldre `Request-Id`-protokollet kan du inaktivera spårnings kontexten med hjälp av den här konfigurationen:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Om du kör en äldre version av SDK rekommenderar vi att du uppdaterar den eller tillämpar följande konfiguration för att aktivera spårnings kontext.

Den här funktionen finns i `Microsoft.ApplicationInsights.AspNetCore` version 2.5.0-beta1 och i `Microsoft.ApplicationInsights.DependencyCollector` version 2.8.0-beta1.
Den är inaktive rad som standard. Om du vill aktivera det anger `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Aktivera stöd för distribuerad W3C-spårning för Java-appar

- **Inkommande konfiguration**

  - För Java EE-appar lägger du till följande i taggen `<TelemetryModules>` i ApplicationInsights. XML:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - För våren Boot Apps lägger du till följande egenskaper:

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
  > Bakåtkompatibla kompatibilitetsläge är aktiverat som standard och parametern `enableW3CBackCompat` är valfri. Använd bara det när du vill inaktivera bakåtkompatibilitet.
  >
  > Vi rekommenderar att du inaktiverar det här när alla dina tjänster har uppdaterats till nyare versioner av SDK: er som stöder W3C-protokollet. Vi rekommenderar starkt att du flyttar till de nya SDK: erna så snart som möjligt.

> [!IMPORTANT]
> Se till att de inkommande och utgående konfigurationerna är exakt desamma.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Aktivera stöd för distribuerad W3C-spårning för webbappar

Den här funktionen finns i `Microsoft.ApplicationInsights.JavaScript`. Den är inaktive rad som standard. Använd `distributedTracingMode` config för att aktivera den. AI_AND_W3C tillhandahålls för bakåtkompatibilitet med alla äldre tjänster som instrumentas av Application Insights.

- **NPM-installation (ignorera om du använder installations programmet för kodfragment)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
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
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>Opentracing och Application Insights

[Specifikationen för Opentracing-datamodellen](https://opentracing.io/) och Application Insights data modeller mappas på följande sätt:

| Application Insights                  | Opentracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` med `span.kind = server`                  |
| `Dependency`                          | `Span` med `span.kind = client`                  |
| `Id` av `Request` och `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` av typen `ChildOf` (överordnat intervall)   |

Mer information finns i [Application Insights telemetri data Model](../../azure-monitor/app/data-model.md).

Definitioner av opentracing-koncept finns i opentracing [Specification](https://github.com/opentracing/specification/blob/master/specification.md) och [semantiska konventioner](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetri korrelation i openräkningar python

Openräkning python följer `OpenTracing` data modell specifikationer som beskrivs tidigare. Det stöder också [W3C-spårnings kontext](https://w3c.github.io/trace-context/) utan att kräva någon konfiguration.

### <a name="incoming-request-correlation"></a>Inkommande begäran-korrelation

Openräkning python korrelerar W3C spårnings kontext rubriker från inkommande begär anden till de intervall som genereras från själva begärandena. Openräkning sker automatiskt med integreringar för dessa populära ramverk för webb program: kolv, Django och Pyramid. Du behöver bara fylla i W3C trace-context-huvudena med [rätt format](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) och skicka dem med begäran. Här är ett exempel på en vattenkolv som demonstrerar detta:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Den här koden kör ett exempel på en program vara på den lokala datorn som lyssnar på port `8080`. Om du vill korrelera spårnings kontexten skickar du en begäran till slut punkten. I det här exemplet kan du använda ett `curl`-kommando:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Genom att titta på [huvud formatet för spårnings kontexten](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)kan du härleda följande information:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Om du tittar på posten i begäran som skickades till Azure Monitor kan du se fält som är ifyllda med spårnings huvud informationen. Du hittar dessa data under loggar (analyser) i Azure Monitor Application Insights resurs.

![Begär telemetri i loggar (analys)](./media/opencensus-python/0011-correlation.png)

`id` fältet har formatet `<trace-id>.<span-id>`där `trace-id` tas från spårnings huvudet som skickades i begäran och `span-id` är en genererad 8-byte-matris för det här intervallet.

`operation_ParentId` fältet har formatet `<trace-id>.<parent-id>`där både `trace-id` och `parent-id` tas från spårnings huvudet som skickades i begäran.

### <a name="log-correlation"></a>Logg korrelation

Med openräkning python kan du korrelera loggar genom att lägga till ett spårnings-ID, ett intervall-ID och en samplings flagga till logg poster. Du lägger till dessa attribut genom att installera [integrering](https://pypi.org/project/opencensus-ext-logging/)av openräknings loggning. Följande attribut kommer att läggas till i python `LogRecord` objekt: `traceId`, `spanId`och `traceSampled`. Observera att detta endast gäller för loggar som skapas efter integrationen.

Här är ett exempel program som demonstrerar detta:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
När den här koden körs skrivs följande ut i-konsolen:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Observera att det finns en `spanId` för logg meddelandet som ligger inom intervallet. Detta är samma `spanId` som tillhör intervallet med namnet `hello`.

Du kan exportera loggdata med hjälp av `AzureLogHandler`. Mer information finns i [den här artikeln](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Telemetri-korrelation i .NET

Med tiden har .NET definierat flera sätt att korrelera telemetri-och diagnostikloggar:

- `System.Diagnostics.CorrelationManager` möjliggör spårning av [LogicalOperationStack och ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` och ETW (Event Tracing for Windows) (ETW) definiera [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) -metoden.
- `ILogger` använder [logg omfattningar](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Kontext spridning för Windows Communication Foundation (WCF) och HTTP-kabelering.

Men dessa metoder aktiverade inte stöd för automatisk Distributed spårning. `DiagnosticSource` stöder automatisk korrelation mellan datorer. .NET-bibliotek stöder `DiagnosticSource` och tillåter automatisk spridning mellan datorer av korrelations kontexten via transporten, till exempel HTTP.

[Användar handboken för aktiviteten](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) i `DiagnosticSource` förklarar grunderna för spårning av aktiviteter.

ASP.NET Core 2,0 stöder extrahering av HTTP-huvuden och start av nya aktiviteter.

`System.Net.Http.HttpClient`, från och med version 4.1.0, stöder automatisk inmatning av korrelations-HTTP-huvuden och spårning av HTTP-anrop som aktiviteter.

Det finns en ny HTTP-modul, [Microsoft. ASPNET. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), för klassisk ASP.net. Den här modulen implementerar telemetri-korrelationen med hjälp av `DiagnosticSource`. Den startar en aktivitet baserat på inkommande begärandehuvuden. Den korrelerar också telemetri från olika stadier av bearbetning av begär Anden, även när alla steg i Internet Information Services (IIS) bearbetning körs i en annan hanterad tråd.

Application Insights SDK, från och med version 2.4.0-beta1, använder `DiagnosticSource` och `Activity` för att samla in telemetri och associera den med den aktuella aktiviteten.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetri korrelation i Java SDK

[Application Insights SDK för Java](../../azure-monitor/app/java-get-started.md) version 2.0.0 eller senare har stöd för automatisk korrelation av telemetri. Den fyller automatiskt i `operation_id` för all telemetri (t. ex. spår, undantag och anpassade händelser) som utfärdats inom omfånget för en begäran. Den sprider också korrelations rubrikerna (beskrivs ovan) för tjänst-till-tjänst-anrop via HTTP, om [Java SDK-agenten](../../azure-monitor/app/java-agent.md) har kon figurer ATS.

> [!NOTE]
> Endast anrop som görs via Apache HttpClient stöds för korrelations funktionen. Både våren RestTemplate och Feign kan användas med Apache HttpClient under huven.

För närvarande stöds inte automatisk kontext spridning mellan meddelande tekniker (t. ex. Kafka, RabbitMQ och Azure Service Bus). Du kan koda sådana scenarier manuellt med hjälp av `trackDependency` och `trackRequest` metoder. I dessa metoder representerar en beroende telemetri ett meddelande som ställs i kö av en tillverkare. Begäran representerar ett meddelande som bearbetas av en konsument. I det här fallet ska både `operation_id` och `operation_parentId` spridas i meddelandets egenskaper.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Telemetri korrelation i asynkrona Java-program

Information om hur du korrelerar telemetri i ett asynkront våren Boot-program finns i [distribuerad spårning i asynkrona Java-program](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications). Den här artikeln innehåller rikt linjer för att instrumentera våren [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) och [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html).


<a name="java-role-name"></a>
## <a name="role-name"></a>Rollnamn

Du kanske vill anpassa hur komponent namn visas i [program kartan](../../azure-monitor/app/app-map.md). Om du vill göra det kan du ange `cloud_RoleName` manuellt genom att vidta någon av följande åtgärder:

- Med Application Insights Java SDK 2.5.0 och senare kan du ange `cloud_RoleName` genom att lägga till `<RoleName>` till din ApplicationInsights. XML-fil:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Om du använder våren boot med Application Insights våren Boot starter, behöver du bara ange ditt anpassade namn för programmet i filen Application. Properties:

  `spring.application.name=<name-of-app>`

  Start programmet för fjäder start tilldelar automatiskt `cloudRoleName` till det värde som du anger för egenskapen `spring.application.name`.

## <a name="next-steps"></a>Nästa steg

- Skriv [anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md).
- Avancerade korrelations scenarier i ASP.NET Core-och ASP.NET finns i [spåra anpassade åtgärder](custom-operations-tracking.md).
- Läs mer om hur du [ställer in cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) för andra SDK: er.
- Publicera alla komponenter i mikrotjänsten på Application Insights. Kolla in de [plattformar som stöds](../../azure-monitor/app/platforms.md).
- Se [data modellen](../../azure-monitor/app/data-model.md) för Application Insights typer.
- Lär dig hur du [utökar och filtrerar telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Granska [Application Insights config-referensen](configuration-with-applicationinsights-config.md).
