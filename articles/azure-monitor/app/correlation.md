---
title: Azure Application Insights-telemetrikorrelation | Microsoft Docs
description: Telemetrikorrelation för Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: cc2d45aee170517d7e41cbda6d92bc21067732d1
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493645"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetrikorrelation i Application Insights

I en värld av mikrotjänster kräver varje logisk åtgärd arbetet som ska utföras i olika komponenter i tjänsten. Var och en av dessa komponenter kan övervakas separat av [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Webbapp komponenten kommunicerar med autentisering-providerns komponent att validera användarens autentiseringsuppgifter och med API-komponenten för att hämta data för visualisering. API-komponent kan fråga efter data från andra tjänster och använda cache-provider-komponenter för att meddela komponenten fakturering om det här anropet. Application Insights har stöd för distribuerade telemetrikorrelation, som du använder för att identifiera komponent som ansvarar för fel eller försämrade prestanda.

Den här artikeln beskriver den datamodell som används av Application Insights för att korrelera telemetri som skickas av flera komponenter. Den behandlar kontext spridning metoder och protokoll. Den behandlar också implementeringen av korrelation koncept på olika språk och plattformar.

## <a name="data-model-for-telemetry-correlation"></a>Datamodell för telemetrikorrelation

Application Insights definierar en [datamodellen](../../azure-monitor/app/data-model.md) för distribuerade telemetrikorrelation. Om du vill associera telemetri med åtgärden logiska varje telemetriobjekt har en kontextfält med namnet `operation_Id`. Den här identifieraren är gemensam för alla telemetriobjekt i distribuerade spårningen. Så även med förlust av telemetri från ett lager, kan du fortfarande associera telemetri som rapporterats av andra komponenter.

En distribuerad logisk åtgärd består vanligtvis av en uppsättning mindre åtgärder som är begäranden som bearbetas av en av komponenterna. Dessa åtgärder definieras av [begär telemetri](../../azure-monitor/app/data-model-request-telemetry.md). Varje begärandetelemetri har sin egen `id` som identifierar det unikt och globalt. Och alla telemetri objekt (till exempel spårningar och undantag) som är associerade med den här begäran bör ange den `operation_parentId` till värdet för begäran `id`.

Varje utgående åtgärd, till exempel ett HTTP-anrop till en annan komponent, representeras av [beroendetelemetri](../../azure-monitor/app/data-model-dependency-telemetry.md). Beroendetelemetri definierar också sin egen `id` som är globalt unikt. Begäran om telemetri, initieras av det här beroendeanropet används `id` som dess `operation_parentId`.

Du kan skapa en vy över distribuerade logiska igen med hjälp av `operation_Id`, `operation_parentId`, och `request.id` med `dependency.id`. De här fälten också definiera orsakssamband ordningen för telemetri-anrop.

Spårningar från komponenter kan gå till olika lagringsenheter objekt i en miljö med mikrotjänster. Varje komponent kan ha sin egen instrumenteringsnyckeln i Application Insights. Om du vill hämta telemetri för den logiska åtgärden, måste du fråga data från varje lagringsenheter. När antalet objekt som lagring är enorm, måste en ledtråd om var du vill titta lite närmare. Application Insights-datamodellen definierar två fält för att lösa problemet: `request.source` och `dependency.target`. Det första fältet identifierar den komponent som initierade beroende begäran och andra identifierar vilken komponent returnerade svaret beroende-anropet.

## <a name="example"></a>Exempel

Låt oss ta ett exempel på ett program som kallas lager priser, som visar aktuella marknad priset för ett lager med hjälp av en extern API som kallas `Stock`. Priserna för lager-programmet har en sida med namnet `Stock page` som klientens webbläsare öppnas med hjälp av `GET /Home/Stock`. Program-frågor i `Stock` API med hjälp av ett HTTP-anrop `GET /api/stock/value`.

Du kan analysera telemetri som resulterande genom att köra en fråga:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Observera att alla objekt i telemetrin dela roten i resultaten `operation_Id`. När ett Ajax-anrop görs från sidan, ett nytt unikt ID (`qJSXU`) tilldelas telemetri om beroenden och ID för sidvisningar används som `operation_ParentId`. Serverförfrågan använder sedan Ajax-ID som `operation_ParentId`.

| ItemType   | namn                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Lagerartiklar sidan                |              | STYz               | STYz         |
| beroende | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| begäran    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| beroende | Hämta /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

När anropet `GET /api/stock/value` görs till en extern tjänst som du vill veta identiteten för den servern där du kan ange den `dependency.target` fältet på rätt sätt. När den externa tjänsten inte stöder övervakning, `target` anges till namnet på tjänsten (till exempel `stock-prices-api.com`). Men om tjänsten identifierar sig genom att returnera ett HTTP-huvud som fördefinierade `target` innehåller tjänstidentiteten där Application Insights för att skapa en distribuerad spårning genom att fråga telemetri från tjänsten.

## <a name="correlation-headers"></a>Korrelations-huvuden

Vi arbetar på en RFC-förslag för den [korrelation HTTP-protokollet](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Förslaget definierar två rubriker:

- `Request-Id`: Innebär globalt unikt ID för anropet.
- `Correlation-Context`: Innebär namn-värdepar insamlingen av egenskaper för distribuerade spår.

Standarden definierar också två scheman för `Request-Id` generation: platta och hierarkiska. Med fast schema, en välkänd `Id` nyckel definieras för den `Correlation-Context` samling.

Application Insights definierar den [tillägget](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) för korrelation HTTP-protokollet. Den använder `Request-Context` namn / värde-par för sprida insamlingen av egenskaper som används av den omedelbara anroparen eller mottagaren. Application Insights SDK använder den här rubriken för att ange `dependency.target` och `request.source` fält.

### <a name="w3c-distributed-tracing"></a>W3C distribuerad spårning

Vi övergår till [W3C distribuerad spårning format](https://w3c.github.io/trace-context/). Den definierar:

- `traceparent`: Utför den globalt unika åtgärds-ID och en unik identifierare för anropet.
- `tracestate`: Innehåller spårning av systemspecifika kontext.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Aktivera stöd för W3C distribuerad spårning för klassiska ASP.NET-appar

Den här funktionen är tillgänglig i `Microsoft.ApplicationInsights.Web` och `Microsoft.ApplicationInsights.DependencyCollector` paket från och med version 2.8.0-beta1.
Det är inaktiverat som standard. Aktivera det genom att ändra `ApplicationInsights.config`:

- Under `RequestTrackingTelemetryModule`, lägga till den `EnableW3CHeadersExtraction` element med värdet satt till `true`.
- Under `DependencyTrackingTelemetryModule`, lägga till den `EnableW3CHeadersInjection` element med värdet satt till `true`.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Aktivera stöd för W3C distribuerad spårning för ASP.NET Core-appar

Den här funktionen är `Microsoft.ApplicationInsights.AspNetCore` version 2.5.0-beta1 och i `Microsoft.ApplicationInsights.DependencyCollector` version 2.8.0-beta1.
Det är inaktiverat som standard. Aktivera det genom att ange `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` till `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Aktivera W3C distribuerad spårning av stöd för Java-appar

- **Inkommande konfiguration**

  - Lägg till följande till för Java EE-appar i `<TelemetryModules>` tagg i ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Lägg till följande egenskaper för Spring Boot-appar:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Utgående konfiguration**

  Lägg till följande AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Bakåtkompatibilitet läge är aktiverat som standard och `enableW3CBackCompat` parametern är valfri. Använda den bara när du vill inaktivera bakåtkompatibilitet.
  >
  > Vi rekommenderar skulle du inaktivera detta när alla tjänster har uppdaterats till nyare versioner av SDK: er som stöder W3C-protokollet. Vi rekommenderar starkt att du flyttar till dessa nyare SDK: er så snart som möjligt.

> [!IMPORTANT]
> Kontrollera att både inkommande och utgående konfigurationer är exakt samma.

## <a name="opentracing-and-application-insights"></a>OpenTracing och Application Insights

Den [OpenTracing datamodelldata specifikationen](https://opentracing.io/) och Application Insights-datamodeller mappa på följande sätt:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`,  `PageView`                 | `Span` med  `span.kind = server`                  |
| `Dependency`                          | `Span` med  `span.kind = client`                  |
| `Id` av `Request` och `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` av typen `ChildOf` (den överordnade span)   |

Mer information finns i den [datamodellen för Application Insights telemetry](../../azure-monitor/app/data-model.md). 

Definitioner av OpenTracing begrepp finns i OpenTracing [specifikationen](https://github.com/opentracing/specification/blob/master/specification.md) och [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Telemetrikorrelation i .NET

Framöver kommer definierats .NET att korrelera loggar telemetri och diagnostik på flera olika sätt:

- `System.Diagnostics.CorrelationManager` gör att spåra [LogicalOperationStack och ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` och för Windows ETW (Event Tracing) definierar den [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) metod.
- `ILogger` använder [Log scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) och HTTP under överföring upp ”aktuella” kontexten spridning.

Dessa metoder aktiverar dock inte stöd för automatisk distribuerad spårning. `DiagnosticSource` är ett sätt att stödja automatisk mellan datorn korrelation. .NET-bibliotek stöder 'DiagnosticSource' och Tillåt automatisk mellan datorn spridningen av Korrelations-kontexten via transport, till exempel HTTP.

Den [guide till aktiviteter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) i `DiagnosticSource` förklarar grunderna med att spåra aktiviteter.

ASP.NET Core 2.0 har stöd för extrahering av HTTP-huvuden och starta en ny aktivitet.

`System.Net.HttpClient`, från och med version 4.1.0, stöder automatisk inmatning av korrelation HTTP-huvuden och spårning av HTTP-anrop som en aktivitet.

Det finns en ny HTTP-modul [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), för klassiska ASP.NET. Den här modulen implementerar telemetrikorrelation med hjälp av `DiagnosticSource`. Den startar en aktivitet som baserat på inkommande begärandehuvuden. Det kan även korrelera telemetri från de olika stegen i behandling av begäranden, även för fall när varje steg i processen för Internet Information Services (IIS) körs på en annan hanterad tråd.

Application Insights SDK, från och med version 2.4.0-beta1 använder `DiagnosticSource` och `Activity` att samla in telemetri och koppla den till den aktuella aktiviteten.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetrikorrelation i Java SDK

Den [Application Insights SDK för Java](../../azure-monitor/app/java-get-started.md) stöder automatisk korrelation av telemetri från och med version 2.0.0. Fyller automatiskt `operation_id` för all telemetri (till exempel spårningar, undantag och anpassade händelser) som utfärdade inom omfånget för en begäran. Den också tar hand om sprider korrelation rubrikerna (beskrivs ovan) för tjänst-till-tjänst-anrop via HTTP, om den [Java SDK-agenten](../../azure-monitor/app/java-agent.md) har konfigurerats.

> [!NOTE]
> Endast de anrop som görs via Apache HTTPClient har stöd för funktionen korrelation. Om du använder Spring RestTemplate eller Feign användas både med Apache HTTPClient i lösningen.

För närvarande stöds automatisk kontext spridning över meddelandetekniker (sådana Kafka, RabbitMQ eller Azure Service Bus) inte. Det är dock möjligt att skriva kod sådana scenarier manuellt med hjälp av den `trackDependency` och `trackRequest` API: er. I dessa API: er, en beroendetelemetri representerar ett meddelande som köas genom en producent och begäran representerar ett meddelande som bearbetas av en konsument. I det här fallet både `operation_id` och `operation_parentId` ska spridas i meddelandets egenskaper.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Telemetrikorrelation i asynkrona Java-program

För att korrelera telemetri i asynkrona Spring Boot-program genom att följa [detta](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) uttömmande artikeln. Det innehåller vägledning för instrumentering Spring's [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) samt [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Rollnamn

Ibland kanske du vill anpassa visningen av Komponentnamn i den [Programkartan](../../azure-monitor/app/app-map.md). Om du vill göra det, kan du manuellt ange den `cloud_RoleName` genom att göra något av följande:

- Om du använder Spring Boot med Application Insights Spring Boot starter är den enda önskade ändringen att ange ditt eget namn för programmet i application.properties-filen.

  `spring.application.name=<name-of-app>`

  Spring Boot starter tilldelar automatiskt `cloudRoleName` att du har angett för den `spring.application.name` egenskapen.

- Om du använder den `WebRequestTrackingFilter`, `WebAppNameContextInitializer` anger programnamnet automatiskt. Lägg till följande i konfigurationsfilen (ApplicationInsights.xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Om du använder kontextklassen molnet:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Nästa steg

- Skriva [anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md).
- Läs mer om [inställningen cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud_rolename) för andra SDK: er.
- Publicera alla komponenter i din mikrotjänst i Application Insights. Kolla in den [plattformar som stöds](../../azure-monitor/app/platforms.md).
- Se den [datamodellen](../../azure-monitor/app/data-model.md) för Application Insights-typer.
- Lär dig hur du [utöka och filtrera telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Granska den [referens för Application Insights-config](configuration-with-applicationinsights-config.md).
