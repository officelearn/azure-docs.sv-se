---
title: Azure Application Insights Telemetrikorrelation | Microsoft Docs
description: Telemetrikorrelation för Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/09/2018
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: d9b6f5c08eed5efceafc71feaf654ad8f4fcafa0
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341131"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetrikorrelation i Application Insights

I en värld av mikrotjänster kräver varje logisk åtgärd jobbet klart på olika komponenter i tjänsten. Var och en av de här komponenterna separat kan övervakas av [Application Insights](app-insights-overview.md). Webbkomponenten appen kommunicerar med autentisering providerns komponent för att verifiera användarens autentiseringsuppgifter och med API-komponenten för att hämta data för visualisering. API-komponenten i sin tur kan fråga efter data från andra tjänster använder cache-provider-komponenter och meddela komponenten fakturering om det här anropet. Application Insights har stöd för distribuerade telemetrikorrelation. Det kan du identifiera vilka komponenten är ansvarig för fel eller försämrade prestanda.

Den här artikeln beskriver den datamodell som används av Application Insights för att korrelera telemetri som skickas av flera komponenter. Den behandlar kontext spridning metoder och protokoll. Den behandlar också implementeringen av Korrelations-koncept på olika språk och plattformar.

## <a name="telemetry-correlation-data-model"></a>Datamodell för telemetri korrelation

Application Insights definierar en [datamodellen](application-insights-data-model.md) för distribuerade telemetrikorrelation. Om du vill associera telemetri med åtgärden logiska varje telemetriobjekt har en kontextfält med namnet `operation_Id`. Den här identifieraren är gemensam för alla telemetriobjekt i distribuerade spårningen. Och med förlust av telemetri från ett lager kan fortfarande du associera telemetri som rapporterats av andra komponenter.

Distribuerad logisk åtgärd består vanligtvis av en uppsättning mindre operations - begäranden som bearbetas av en av komponenterna. Dessa åtgärder definieras av [begär telemetri](application-insights-data-model-request-telemetry.md). Varje begärandetelemetri har sin egen `id` som identifierar det unikt globalt. Och all telemetri - spårningar, undantag och annat som är associerade med den här begäran bör ange den `operation_parentId` till värdet för begäran `id`.

Varje utgående åtgärd som http-anrop till en annan komponent som representeras av [beroendetelemetri](application-insights-data-model-dependency-telemetry.md). Beroendetelemetri definierar också sin egen `id` som är globalt unikt. Begärandetelemetri initieras av det här beroendeanropet använder den som `operation_parentId`.

Du kan skapa vy över distribuerade logiska importförloppet med hjälp av `operation_Id`, `operation_parentId`, och `request.id` med `dependency.id`. Dessa fält kan du också definiera orsakssamband ordningen för telemetri-anrop.

Spårningar från komponenter kan gå till olika lagringsutrymmen i micro-tjänster. Alla komponenter kan ha sin egen instrumenteringsnyckeln i Application Insights. Om du vill hämta telemetri för den logiska åtgärden, måste du köra frågor mot data från varje storage. När antalet lagringsutrymmen är stor, måste ha ett tips på var du vill titta lite närmare.

Application Insights datamodellen definierar två fält för att lösa problemet: `request.source` och `dependency.target`. Det första fältet identifierar den komponent som initierade beroende begäran och andra identifierar vilken komponent returnerade svaret beroende-anropet.


## <a name="example"></a>Exempel

Låt oss ta ett exempel på ett program lager priser som visar det aktuella marknad priset aktier externt API kallas aktier API. PRISER för lager-programmet har en sida `Stock page` öppnas av klienten web browser använder `GET /Home/Stock`. Programmet frågar lager-API med hjälp av ett HTTP-anrop `GET /api/stock/value`.

Du kan analysera resulterande telemetri som en fråga som körs:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

I Obs visa resultat som att alla objekt i telemetrin dela roten `operation_Id`. När ajax-anrop görs från sidan – nytt unikt id `qJSXU` tilldelas telemetri om beroenden och Sidvisningars id används som `operation_ParentId`. Serverbegäran använder i sin tur ajax's-id som `operation_ParentId`osv.

| ItemType   | namn                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| sidvisningar   | Lagerartiklar sidan                |              | STYz               | STYz         |
| beroende | GET/Home/Stock           | qJSXU        | STYz               | STYz         |
| begäran    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| beroende | Hämta /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

Nu när anropet `GET /api/stock/value` görs till en extern tjänst som du vill veta identiteten för servern. Där du kan ange `dependency.target` fältet på rätt sätt. När den externa tjänsten inte stöder övervakning – `target` anges till namnet på tjänsten som `stock-prices-api.com`. Men om tjänsten identifierar sig genom att returnera en fördefinierad HTTP-huvud - `target` innehåller tjänstidentiteten där Application Insights för att skapa distribuerade spårning genom att fråga telemetri från tjänsten. 

## <a name="correlation-headers"></a>Korrelations-huvuden

Vi arbetar på RFC förslag för den [korrelation HTTP-protokollet](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Förslaget definierar två rubriker:

- `Request-Id` utföra ett globalt unikt id för anropet
- `Correlation-Context` -utföra namn värde-par insamlingen av egenskaper för distribuerad spårning

Standarden definierar också två scheman för `Request-Id` generation - platta och hierarkiska. Med fast schema, det är ett välkänt `Id` key som definierats för den `Correlation-Context` samling.

Application Insights definierar den [tillägget](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) för korrelation HTTP-protokollet. Den använder `Request-Context` namn/värdepar för sprida insamlingen av egenskaper som används av den omedelbara anroparen eller mottagaren. Application Insights SDK använder den här rubriken för att ange `dependency.target` och `request.source` fält.

### <a name="w3c-distributed-tracing"></a>W3C distribuerad spårning

Vi övergår till (W3C distribuerad spårning format) [https://w3c.github.io/distributed-tracing/report-trace-context.html]. Den definierar:
- `traceparent` -globalt unika åtgärds-id och unik identifierare för anropet
- `tracestate` -innebär spårning av specifika systemkontexten.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-classic-apps"></a>Aktivera stöd för W3C distribuerad spårning för ASP.NET, klassisk appar

Den här funktionen är tillgänglig i Microsoft.ApplicationInsights.Web och Microsoft.ApplicationInsights.DependencyCollector paket från och med version 2.8.0-beta1.
Det är **av** som standard om du vill göra det måste ändra `ApplicationInsights.config`:

* under `RequestTrackingTelemetryModule` lägga till `EnableW3CHeadersExtraction` element med värdet satt till `true`
* under `DependencyTrackingTelemetryModule` lägga till `EnableW3CHeadersInjection` element med värdet satt till `true`

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Aktivera stöd för W3C distribuerad spårning för ASP.NET Core-appar

Den här funktionen är Microsoft.ApplicationInsights.AspNetCore med version 2.5.0-beta1 och Microsoft.ApplicationInsights.DependencyCollector version 2.8.0-beta1.
Det är **av** så att den som standard `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` till `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

## <a name="open-tracing-and-application-insights"></a>Öppna spårning och Application Insights

Den [öppen spårning datamodelldata specifikationen](http://opentracing.io/) och Application Insights-datamodeller mappa på följande sätt:

| Application Insights                  | Öppna spårning                                      |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` med `span.kind = server`                  |
| `Dependency`                          | `Span` med `span.kind = client`                  |
| `Id` av `Request` och `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` av typen `ChildOf` (den överordnade span)   |

Läs mer på Application Insights-datamodell, [datamodellen](application-insights-data-model.md). 

Se öppen spårning [specifikationen](https://github.com/opentracing/specification/blob/master/specification.md) och [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) för definitioner av öppen spårning begrepp.


## <a name="telemetry-correlation-in-net"></a>Telemetrikorrelation i .NET

.NET definierats flera olika sätt att korrelera telemetri och diagnostik loggar över tid. Det finns `System.Diagnostics.CorrelationManager` så att du kan spåra [LogicalOperationStack och ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` och Windows ETW definiera metoden [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` använder [Log scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF- och Http under överföring upp ”aktuella” kontexten spridning.

Men inte aktivera automatisk distribuerad spårning stöd för dessa metoder. `DiagnosticsSource` är ett sätt att stödja automatisk mellan datorn korrelation. .NET-bibliotek stöder diagnostik och Tillåt automatisk mellan datorn spridningen av Korrelations-kontexten via transport som http.

Den [guide till aktiviteter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) förklarar grunderna med att spåra aktiviteter i diagnostik källan. 

ASP.NET Core 2.0 har stöd för extrahering av Http-huvuden och starta den nya aktiviteten. 

`System.Net.HttpClient` startversion `4.1.0` stöder automatisk inmatning för korrelationen Http-huvuden och spåra http-anrop som en aktivitet.

Det finns en ny Http-modul [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) för ASP.NET-klassisk. Den här modulen implementerar telemetrikorrelation med DiagnosticsSource. Startar aktivitet baserat på inkommande begärandehuvuden. Det kan även korrelera telemetri från olika faser av begäranbearbetningen. Även för fall när varje steg i IIS-processen körs på en annan hantera trådar.

Application Insights SDK startversion `2.4.0-beta1` använder DiagnosticsSource och aktiviteten för att samla in telemetri och associera den med den aktuella aktiviteten. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetrikorrelation i Java SDK
Den [Application Insights Java SDK](app-insights-java-get-started.md) stöder automatisk korrelation av telemetri från och med version `2.0.0`. Fyller automatiskt `operation_id` för all telemetri (spårningar, undantag, anpassade händelser osv.) som utfärdade inom omfånget för en begäran. Den också tar hand om sprider Korrelations-huvuden (beskrivs ovan) för tjänst till tjänst-anrop via HTTP om den [Java SDK-agenten](app-insights-java-agent.md) har konfigurerats. Obs: endast anrop som görs via Apache HTTP-klienten har stöd för funktionen korrelation. Om du använder Spring Rest mall eller Feign användas både med Apache HTTP-klient under huven.

För närvarande stöds inte automatisk kontext spridning över meddelandetekniker (t.ex. Kafka, RabbitMQ, Azure Service Bus). Det är möjligt, men att skriva kod sådana scenarier manuellt med hjälp av den `trackDependency` och `trackRequest` API: er, där en beroendetelemetri representerar ett meddelande som köas genom en producent och begäran representerar ett meddelande som bearbetas av en konsument. I det här fallet både `operation_id` och `operation_parentId` ska spridas i meddelandets egenskaper.

<a name="java-role-name"></a>
### <a name="role-name"></a>Rollnamn
Ibland kanske du vill anpassa visningen av Komponentnamn i den [Programkartan](app-insights-app-map.md). Om du vill göra det, kan du manuellt ange den `cloud_roleName` genom att göra något av följande:

Via en telemetri-initierare (alla telemetri-objekt är märkta)
```Java
public class CloudRoleNameInitializer extends WebTelemetryInitializerBase {

    @Override
    protected void onInitializeTelemetry(Telemetry telemetry) {
        telemetry.getContext().getTags().put(ContextTagKeys.getKeys().getDeviceRoleName(), "My Component Name");
    }
  }
```
Via den [kontext enhetsklass](https://docs.microsoft.com/et-ee/java/api/com.microsoft.applicationinsights.extensibility.context._device_context) (endast den här telemetriobjekt är märkta)
```Java
telemetry.getContext().getDevice().setRoleName("My Component Name");
```

## <a name="next-steps"></a>Nästa steg

- [Skriva anpassad telemetri](app-insights-api-custom-events-metrics.md)
- Publicera alla komponenter i din micro tjänst med Application Insights. Kolla in [plattformar som stöds](app-insights-platforms.md).
- Se [datamodellen](application-insights-data-model.md) för Application Insights och modellen.
- Lär dig hur du [utöka och filtrera telemetri](app-insights-api-filtering-sampling.md).
- [Application Insights config-referens](app-insights-configuration-with-applicationinsights-config.md)

