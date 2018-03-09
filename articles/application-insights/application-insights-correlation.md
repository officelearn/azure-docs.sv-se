---
title: Azure Application Insights telemetri korrelation | Microsoft Docs
description: Application Insights telemetri korrelation
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: 5d4abbf8194d633305877275e3dd273352906ad3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="telemetry-correlation-in-application-insights"></a>Korrelation telemetri i Application Insights

Alla logiska åtgärden kräver i världen micro tjänster arbete i olika komponenter i tjänsten. Var och en av de här komponenterna separat kan övervakas av [Programinsikter](app-insights-overview.md). Webbkomponenten appen kommunicerar med autentisering providerns komponent för att verifiera autentiseringsuppgifter och med komponenten API för att hämta data för visualisering. API-filkomponenten i sin tur kan fråga efter data från andra tjänster och använda cache-provider-komponenter och meddela komponenten faktureringsinformation om det här anropet. Application Insights stöder distribuerade telemetri korrelation. Det gör att du kan identifiera vilken komponent som ansvarar för fel eller prestandaförsämring.

Den här artikeln förklarar datamodellen som används av Application Insights för att korrelera telemetri som skickas av flera komponenter. Den omfattar kontexten spridningen metoder och protokoll. Den omfattar också implementeringen av korrelation begrepp på olika språk och plattformar.

## <a name="telemetry-correlation-data-model"></a>Telemetri Korrelations-datamodell

Application Insights definierar en [datamodellen](application-insights-data-model.md) för distribuerade telemetri korrelation. Om du vill associera telemetri med logiska åtgärden varje telemetri objekt har en kontextfält med namnet `operation_Id`. Den här identifieraren delas av alla telemetri objekt i distribuerade spårningen. Så även om förlust av telemetri från ett enda lager kan fortfarande du associera telemetri som rapporterats av andra komponenter.

Distribuerad logisk åtgärd består vanligtvis av en uppsättning mindre operations - begäranden som bearbetas av en av komponenterna. Dessa åtgärder har definierats av [begära telemetri](application-insights-data-model-request-telemetry.md). Varje begärandetelemetri har sin egen `id` som globalt unikt identifierar. Och all telemetri - spårningar, undantag, etc. som är associerade med den här begäran måste ange den `operation_parentId` till värdet för begäran `id`.

Varje utgående åtgärd som HTTP-anrop till en annan komponent som representeras av [beroendetelemetri](application-insights-data-model-dependency-telemetry.md). Beroendetelemetri definierar också sin egen `id` som är globalt unikt. Begärandetelemetri initieras av det här beroendeanropet används som `operation_parentId`.

Du kan skapa vy över distribuerade logiska åtgärden med hjälp av `operation_Id`, `operation_parentId`, och `request.id` med `dependency.id`. De här fälten också definiera orsakssamband ordningen på telemetri anrop.

Spår från komponenter kan gå till olika lagringsobjekt i micro services-miljön. Varje komponent kan ha sin egen instrumentation nyckel i Application Insights. Om du vill hämta telemetri för den logiska åtgärden måste du fråga efter data från varje lagring. När antalet lagringsobjekt är stort, måste du ha ett tips på var nästa.

Application Insights-datamodell definierar två fält för att lösa problemet: `request.source` och `dependency.target`. Det första fältet identifierar den komponent som initierade beroende begäran och andra identifierar vilken komponent returnerade svar för beroende samtalet.


## <a name="example"></a>Exempel

Låt oss ta ett exempel på ett program BÖRS priser som visar aktuella marknaden priset på en börs externt API kallas LAGERLISTA API. De priser som BÖRS programmet har en sida `Stock page` öppnas av klienten web webbläsare använder `GET /Home/Stock`. Programmet frågar BÖRS-API med hjälp av en HTTP-anrop `GET /api/stock/value`.

Du kan analysera resulterande telemetri som kör en fråga:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

I resultatet visa anteckningen som delar alla telemetri objekt roten `operation_Id`. När ajax-anrop görs från sidan - nytt unikt id `qJSXU` har tilldelats beroendetelemetri och Pageview's id används som `operation_ParentId`. I sin tur serverbegäran använder ajax's id som `operation_ParentId`osv.

| itemType   | namn                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Lagrets sida                |              | STYz               | STYz         |
| beroende | GET/Home/Stock           | qJSXU        | STYz               | STYz         |
| Begäran    | GET-Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| beroende | Hämta /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Nu när anropet `GET /api/stock/value` görs till en extern tjänsten som du vill veta identiteten för servern. Du kan ange `dependency.target` fältet på lämpligt sätt. När den externa tjänsten inte stöder övervakning - `target` har angetts till värdnamnet för tjänsten som `stock-prices-api.com`. Men om tjänsten identifierar sig genom att returnera en fördefinierad HTTP-huvudet - `target` innehåller tjänstidentiteten som tillåter Application Insights att skapa distribuerade spårning genom att fråga telemetri från tjänsten. 

## <a name="correlation-headers"></a>Huvuden för korrelation

Vi arbetar på RFC förslag för den [korrelation HTTP-protokollet](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Den här förslag definierar två huvuden:

- `Request-Id` utföra globalt unikt id för anropet
- `Correlation-Context` -utföra namn värde-par mängden distribuerade trace-egenskaper

Standarden definierar också två scheman för `Request-Id` generation - platta och hierarkiska. Flat schemat, det är en välkänd `Id` key som definierats för den `Correlation-Context` samling.

Application Insights definierar den [tillägget](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) för korrelation HTTP-protokollet. Den använder `Request-Context` namn-värde-par att sprida insamlingen av egenskaper som används av den omedelbara anroparen eller mottagarmetoderna. Det här sidhuvudet används i Application Insights SDK för att ange `dependency.target` och `request.source` fält.

## <a name="open-tracing-and-application-insights"></a>Öppna spårning och Application Insights

[Öppna spårning](http://opentracing.io/) och Application Insights data modeller ser ut 

- `request`, `pageView` mappar till **Span** med `span.kind = server`
- `dependency` mappar till **Span** med `span.kind = client`
- `id` för en `request` och `dependency` mappar till **Span.Id**
- `operation_Id` mappar till **TraceId**
- `operation_ParentId` mappar till **referens** av typen `ChildOf`

Se [datamodellen](application-insights-data-model.md) för Application Insights typer och modell.

Se [specifikationen](https://github.com/opentracing/specification/blob/master/specification.md) och [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) definitioner av öppna spårning begrepp.


## <a name="telemetry-correlation-in-net"></a>Telemetri korrelation i .NET

.NET definierats flera olika sätt att korrelera telemetri och diagnostik loggar över tid. Det finns `System.Diagnostics.CorrelationManager` att tillåta för att spåra [LogicalOperationStack och ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` och Windows ETW definiera metoden [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` använder [loggen scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF- och HTTP-överföring in ”aktuella” kontexten spridning.

De här metoderna inte men aktivera stöd för automatisk distribuerade spårning. `DiagnosticsSource` är ett sätt att stödja automatisk mellan datorn korrelation. .NET-biblioteken stöder diagnostik och Tillåt automatisk mellan datorn spridning av korrelation kontexten via transport som http.

Den [guide till aktiviteter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) förklarar grunderna i Aktivitetsspårning i diagnostik källan. 

ASP.NET Core 2.0 stöder extrahering av Http-huvuden och sedan starta den nya aktiviteten. 

`System.Net.HttpClient` startversion `4.1.0` stöder automatisk injektion av korrelationen Http-huvuden och spåra http-anrop som en aktivitet.

Det finns en ny Http-modul [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) för ASP.NET-klassisk. Den här modulen implementerar telemetri korrelation med DiagnosticsSource. Startar aktivitet baserat på inkommande begärandehuvuden. Den också korrelerar telemetri från de olika stegen i behandling av begäranden. Även i fall när varje steg i processen för IIS körs på en annan hantera trådar.

Application Insights SDK startversion `2.4.0-beta1` använder DiagnosticsSource och aktiviteten för att samla in telemetri och koppla den till den aktuella aktiviteten. 

## <a name="next-steps"></a>Nästa steg

- [Skriv anpassad telemetri](app-insights-api-custom-events-metrics.md)
- Publicera alla komponenter i micro tjänsten på Application Insights. Checka ut [plattformar som stöds av](app-insights-platforms.md).
- Se [datamodellen](application-insights-data-model.md) för Application Insights typer och modell.
- Lär dig hur du [utöka och filtrera telemetri](app-insights-api-filtering-sampling.md).
