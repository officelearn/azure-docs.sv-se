---
title: Telemetrikorrelation för Azure Application Insights | Microsoft-dokument
description: Application Insights telemetrikorrelation
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276132"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetrikorrelation i Application Insights

I en värld av mikrotjänster kräver varje logisk operation arbete som ska utföras i olika komponenter i tjänsten. Du kan övervaka var och en av dessa komponenter separat med hjälp av [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights stöder distribuerad telemetrikorrelation, som du använder för att identifiera vilken komponent som är ansvarig för fel eller prestandaförsämring.

I den här artikeln beskrivs den datamodell som används av Application Insights för att korrelera telemetri som skickas av flera komponenter. Det omfattar tekniker och protokoll för kontextspridning. Det omfattar också genomförandet av korrelationstaktik på olika språk och plattformar.

## <a name="data-model-for-telemetry-correlation"></a>Datamodell för telemetrikorrelation

Application Insights definierar en [datamodell](../../azure-monitor/app/data-model.md) för distribuerad telemetrikorrelation. Om du vill associera telemetri med en logisk åtgärd `operation_Id`har varje telemetriobjekt ett kontextfält som heter . Den här identifieraren delas av alla telemetriobjekt i den distribuerade spårningen. Så även om du förlorar telemetri från ett enda lager kan du fortfarande associera telemetri som rapporterats av andra komponenter.

En distribuerad logisk åtgärd består vanligtvis av en uppsättning mindre åtgärder som bearbetas av en av komponenterna. Dessa åtgärder definieras av [begäran telemetri](../../azure-monitor/app/data-model-request-telemetry.md). Varje begäran telemetri objekt `id` har sin egen som identifierar den unikt och globalt. Och alla telemetriobjekt (t.ex. spårningar och undantag) som `operation_parentId` är associerade med `id`begäran ska ange värdet för begäran .

Varje utgående åtgärd, till exempel ett HTTP-anrop till en annan komponent, representeras av [beroendetelemetri](../../azure-monitor/app/data-model-dependency-telemetry.md). Beroendetelemetri definierar också `id` sin egen som är globalt unik. Begär telemetri, initierad av det här `id` beroendeanropet, använder detta som . `operation_parentId`

Du kan skapa en vy över `operation_Id`den `operation_parentId`distribuerade logiska åtgärden med hjälp av , och `request.id` med `dependency.id`. Dessa fält definierar också orsaksordningen för telemetrianrop.

I en mikrotjänstmiljö kan spårningar från komponenter gå till olika lagringsobjekt. Varje komponent kan ha sin egen instrumenteringsnyckel i Application Insights. För att hämta telemetri för den logiska åtgärden frågar Application Insights data från varje lagringsobjekt. När antalet lagringsobjekt är stort behöver du en ledtråd om var du ska leta härnäst. Datamodellen Application Insights definierar två fält `request.source` för `dependency.target`att lösa problemet: och . Det första fältet identifierar den komponent som initierade beroendebegäran. Det andra fältet identifierar vilken komponent som returnerade svaret för beroendeanropet.

## <a name="example"></a>Exempel

Nu ska vi titta på ett exempel. Ett program som heter Aktiekurser visar det aktuella marknadspriset för en aktie med hjälp av ett externt API som heter Stock. Aktieprisprogrammet har en sida med namnet Lagersida som `GET /Home/Stock`klientwebbläsaren öppnar med hjälp av . Programmet frågar stock-API:et `GET /api/stock/value`med hjälp av HTTP-anropet .

Du kan analysera den resulterande telemetrin genom att köra en fråga:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Observera att alla telemetriobjekt delar roten `operation_Id`i resultatet . När ett Ajax-samtal görs från sidan tilldelas`qJSXU`ett nytt unikt ID ( ) till beroendet telemetri, och `operation_ParentId`ID för pageView används som . Serverbegäran använder sedan Ajax ID som `operation_ParentId`.

| itemType (artikeltyp)   | namn                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| Sidvisning   | Sida för lager                |              | STYz (styz)               | STYz (styz)         |
| Beroende | FÅ /Hem/Lager           | qJSXU (på andra)        | STYz (styz)               | STYz (styz)         |
| Begäran    | FÅ Hem / Lager            | KqKwlrSt9PA= | qJSXU (på andra)              | STYz (styz)         |
| Beroende | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz (styz)         |

När samtalet `GET /api/stock/value` görs till en extern tjänst måste du känna till serverns `dependency.target` identitet så att du kan ange fältet på rätt sätt. När den externa tjänsten inte `target` stöder övervakning ställs den in på tjänstens värdnamn (till exempel `stock-prices-api.com`). Men om tjänsten identifierar sig själv genom att returnera `target` ett fördefinierat HTTP-huvud, innehåller tjänstidentiteten som gör att Application Insights kan skapa en distribuerad spårning genom att fråga telemetri från den tjänsten.

## <a name="correlation-headers"></a>Korrelationsrubriker

Application Insights övergår till [W3C Trace-Context](https://w3c.github.io/trace-context/), som definierar:

- `traceparent`: Bär det globalt unika åtgärds-ID:t och den unika identifieraren för anropet.
- `tracestate`: Bär systemspecifika spårningskontexter.

Den senaste versionen av Program Insights SDK stöder Trace-Context-protokollet, men du kan behöva välja det. (Bakåtkompatibilitet med det tidigare korrelationsprotokollet som stöds av SDK för Application Insights bibehålls.)

[Korrelationen HTTP-protokollet, även kallat Request-Id,](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)håller på att inaktuell. Det här protokollet definierar två rubriker:

- `Request-Id`: Bär det globalt unika ID:t för samtalet.
- `Correlation-Context`: Bär namn-värde par samling av distribuerade spåregenskaper.

Application Insights definierar också [tillägget](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) för korrelations-HTTP-protokollet. Den `Request-Context` använder namn-värde par för att sprida insamling av egenskaper som används av den närmaste anroparen eller callee. Programstatistiken SDK använder det här `dependency.target` `request.source` huvudet för att ange fälten och.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Aktivera stöd för distribuerad W3C-spårning för klassiska ASP.NET-appar
 
  > [!NOTE]
  >  Från `Microsoft.ApplicationInsights.Web` och `Microsoft.ApplicationInsights.DependencyCollector`med och behövs ingen konfiguration.

W3C Trace-Context-stöd implementeras på ett bakåtkompatibelt sätt. Korrelation förväntas fungera med program som är instrumenterade med tidigare versioner av SDK (utan W3C-stöd).

Om du vill fortsätta `Request-Id` använda det äldre protokollet kan du inaktivera Trace-Context med den här konfigurationen:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Om du kör en äldre version av SDK rekommenderar vi att du uppdaterar den eller tillämpar följande konfiguration för att aktivera Trace-Context.
Den här funktionen `Microsoft.ApplicationInsights.Web` är `Microsoft.ApplicationInsights.DependencyCollector` tillgänglig i och paket, som börjar med version 2.8.0-beta1.
Den är inaktiverad som standard. Om du vill aktivera `ApplicationInsights.config`den gör du följande ändringar i:

- Lägg `RequestTrackingTelemetryModule`till `EnableW3CHeadersExtraction` elementet och ange `true`dess värde i .
- Lägg `DependencyTrackingTelemetryModule`till `EnableW3CHeadersInjection` elementet och ange `true`dess värde i .
- Lägg `W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers`till under . Det kommer att se ut ungefär som det här exemplet:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Aktivera stöd för W3C-distribuerad spårning för ASP.NET Core-appar

 > [!NOTE]
  > Från `Microsoft.ApplicationInsights.AspNetCore` och med version 2.8.0 behövs ingen konfiguration.
 
W3C Trace-Context-stöd implementeras på ett bakåtkompatibelt sätt. Korrelation förväntas fungera med program som är instrumenterade med tidigare versioner av SDK (utan W3C-stöd).

Om du vill fortsätta `Request-Id` använda det äldre protokollet kan du inaktivera Trace-Context med den här konfigurationen:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Om du kör en äldre version av SDK rekommenderar vi att du uppdaterar den eller tillämpar följande konfiguration för att aktivera Trace-Context.

Den här `Microsoft.ApplicationInsights.AspNetCore` funktionen finns i version 2.5.0-beta1 och i `Microsoft.ApplicationInsights.DependencyCollector` version 2.8.0-beta1.
Den är inaktiverad som standard. Om du vill `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` `true`aktivera den ställer du in på:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Aktivera W3C-stöd för distribuerad spårning för Java-appar

- **Inkommande konfiguration**

  - För Java EE-appar lägger `<TelemetryModules>` du till följande i taggen i ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - För Spring Boot-appar lägger du till följande egenskaper:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Utgående konfiguration**

  Lägg till följande i AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Bakåtkompatibilitetsläget är aktiverat som standard `enableW3CBackCompat` och parametern är valfri. Använd den bara när du vill inaktivera bakåtkompatibiliteten.
  >
  > Helst skulle du stänga av detta när alla dina tjänster har uppdaterats till nyare versioner av SDK:er som stöder W3C-protokollet. Vi rekommenderar starkt att du flyttar till dessa nyare SDK:er så snart som möjligt.

> [!IMPORTANT]
> Kontrollera att de inkommande och utgående konfigurationerna är exakt desamma.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Aktivera W3C-stöd för distribuerad spårning för webbappar

Den här `Microsoft.ApplicationInsights.JavaScript`funktionen finns i . Den är inaktiverad som standard. Använd `distributedTracingMode` config för att aktivera den. AI_AND_W3C tillhandahålls för bakåtkompatibilitet med alla äldre tjänster som instrumenteras av Application Insights.

- **npm-installation (ignorera om du använder kodavsnittsinställningar)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Kodavsnittsinställningar (ignorera om npm-installation)**

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

## <a name="opentracing-and-application-insights"></a>OpenTracing och Application Insights

[OpenTracing datamodellspecifikation](https://opentracing.io/) och Application Insights datamodeller mappas på följande sätt:

| Application Insights                  | ÖppnaTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span`Med`span.kind = server`                  |
| `Dependency`                          | `Span`Med`span.kind = client`                  |
| `Id`av `Request` och`Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference`av `ChildOf` typen (det överordnade intervallet)   |

Mer information finns i [Telemetridatamodellen Application Insights](../../azure-monitor/app/data-model.md).

Definitioner av OpenTracing-begrepp finns i [OpenTracing-specifikationen](https://github.com/opentracing/specification/blob/master/specification.md) och [semantiska konventioner .](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetrikorrelation i OpenCensus Python

OpenCensus Python följer `OpenTracing` de datamodellspecifikationer som beskrivits tidigare. Den stöder också [W3C Trace-Context](https://w3c.github.io/trace-context/) utan att kräva någon konfiguration.

### <a name="incoming-request-correlation"></a>Korrelation för inkommande begäran

OpenCensus Python korrelerar W3C Trace-Context-huvuden från inkommande begäranden till de intervall som genereras från själva begäranden. OpenCensus kommer att göra detta automatiskt med integrationer för dessa populära webbapplikationramverk: Flask, Django och Pyramid. Du behöver bara fylla i W3C Trace-Context-rubrikerna med [rätt format](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) och skicka dem med begäran. Här är ett prov Flask program som visar detta:

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

Den här koden kör ett exempel Flask program `8080`på din lokala dator, lyssna på port . Om du vill korrelera spårningskontexten skickar du en begäran till slutpunkten. I det här exemplet `curl` kan du använda ett kommando:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Genom att titta på [trace-context-header-formatet](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)kan du härleda följande information:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Om du tittar på den begärandepost som skickades till Azure Monitor kan du se fält som fylls med spårningshuvudinformationen. Du hittar dessa data under Loggar (Analytics) i Azure Monitor Application Insights-resursen.

![Begär telemetri i loggar (Analytics)](./media/opencensus-python/0011-correlation.png)

Fältet `id` är i `<trace-id>.<span-id>`formatet , `trace-id` där tas från spårningshuvudet som `span-id` skickades i begäran och är en genererad 8-byte array för det här intervallet.

Fältet `operation_ParentId` är i `<trace-id>.<parent-id>`formatet , `trace-id` där `parent-id` både och tas från spårningshuvudet som skickades i begäran.

### <a name="log-correlation"></a>Loggkorrelation

OpenCensus Python gör att du kan korrelera loggar genom att lägga till ett spårnings-ID, ett intervall-ID och en samplingsflagga för att logga poster. Du lägger till dessa attribut genom att installera OpenCensus [loggningsintegrering](https://pypi.org/project/opencensus-ext-logging/). Följande attribut läggs till i `LogRecord` Python-objekt: `traceId`, `spanId`och `traceSampled`. Observera att detta endast börjar gälla för loggrar som skapas efter integrationen.

Här är ett exempelprogram som visar detta:

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
När den här koden körs skrivs följande ut i konsolen:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Observera att det `spanId` finns en present till loggmeddelandet som finns inom intervallet. Det här `spanId` är samma som tillhör `hello`intervallet .

Du kan exportera loggdata `AzureLogHandler`med hjälp av . Mer information finns i [den här artikeln](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Telemetrikorrelation i .NET

Med tiden har .NET definierat flera sätt att korrelera telemetri och diagnostikloggar:

- `System.Diagnostics.CorrelationManager`tillåter spårning av [LogicalOperationStack och ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource`och Händelsespårning för Windows (ETW) definierar metoden [SetCurrentThreadActivityId.](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx)
- `ILogger`använder [loggomfattningar](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) och HTTP binder upp "aktuell" kontextspridning.

Men dessa metoder gjorde det inte möjligt för automatisk support för distribuerad spårning. `DiagnosticSource`stöder automatisk korrelation mellan datorer. .NET-bibliotek `DiagnosticSource` stöder och tillåter automatisk spridning mellan datorer av korrelationskontexten via transporten, till exempel HTTP.

Användarhandboken för `DiagnosticSource` [aktivitet](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) i förklarar grunderna i spårningsaktiviteter.

ASP.NET Core 2.0 stöder extrahering av HTTP-huvuden och start av nya aktiviteter.

`System.Net.Http.HttpClient`, som börjar med version 4.1.0, stöder automatisk injektion av korrelations-HTTP-huvuden och spårar HTTP-anrop som aktiviteter.

Det finns en ny HTTP-modul, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), för klassiska ASP.NET. Den här modulen implementerar `DiagnosticSource`telemetrikorrelation med hjälp av . Den startar en aktivitet baserat på inkommande begäranden. Det korrelerar också telemetri från de olika stadierna av begäran bearbetning, även när varje steg i Internet Information Services (IIS) bearbetning körs på en annan hanterad tråd.

Application Insights SDK, som börjar med version 2.4.0-beta1, använder `DiagnosticSource` och `Activity` samlar in telemetri och associerar den med den aktuella aktiviteten.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetrikorrelation i Java SDK

[Application Insights SDK för Java](../../azure-monitor/app/java-get-started.md) version 2.0.0 eller senare stöder automatisk korrelation av telemetri. Den fylls `operation_id` i automatiskt för all telemetri (som spårningar, undantag och anpassade händelser) som utfärdas inom ramen för en begäran. Det sprider också korrelationsrubriker (beskrivs tidigare) för service-till-tjänst-anrop via HTTP, om [Java SDK-agenten](../../azure-monitor/app/java-agent.md) är konfigurerad.

> [!NOTE]
> Endast samtal som görs via Apache HttpClient stöds för korrelationsfunktionen. Både Spring RestTemplate och Feign kan användas med Apache HttpClient under huven.

För närvarande stöds inte automatisk kontextspridning mellan meddelandetekniker (som Kafka, RabbitMQ och Azure Service Bus). Det är möjligt att koda sådana `trackDependency` `trackRequest` scenarier manuellt med hjälp av och metoder. I dessa metoder representerar en beroendetelemetri ett meddelande som enqueueras av en producent. Begäran representerar ett meddelande som bearbetas av en konsument. I det här `operation_id` `operation_parentId` fallet både och bör spridas i meddelandets egenskaper.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Telemetrikorrelation i asynkrona Java-program

Mer information om hur du korrelerar telemetri i ett asynkront springstart-program finns [i Distribuerad spårning i asynkrona Java-program](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications). Denna artikel ger vägledning för instrumentering Vårens [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) och [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html).


<a name="java-role-name"></a>
## <a name="role-name"></a>Rollnamn

Du kanske vill anpassa hur komponentnamn visas i [programöversikten](../../azure-monitor/app/app-map.md). För att göra detta kan `cloud_RoleName` du manuellt ställa in genom att vidta någon av följande åtgärder:

- Med Application Insights Java SDK 2.5.0 och `cloud_RoleName` senare `<RoleName>` kan du ange filen ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Om du använder Spring Boot med Application Insights Spring Boot Starter behöver du bara ange ditt anpassade namn för programmet i filen application.properties:

  `spring.application.name=<name-of-app>`

  Spring Boot Starter tilldelar `cloudRoleName` automatiskt det värde `spring.application.name` du anger för egenskapen.

## <a name="next-steps"></a>Nästa steg

- Skriv [anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md).
- Avancerade korrelationsscenarier i ASP.NET Core och ASP.NET finns i [Spåra anpassade åtgärder](custom-operations-tracking.md).
- Läs mer om [hur du ställer in cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) för andra SDK:er.
- Ombord på alla komponenter i din mikrotjänst på Application Insights. Kolla in plattformar som [stöds](../../azure-monitor/app/platforms.md).
- Se [datamodellen](../../azure-monitor/app/data-model.md) för application insights-typer.
- Lär dig hur du [utökar och filtrerar telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Granska [konfigurationsreferensen för Application Insights](configuration-with-applicationinsights-config.md).
