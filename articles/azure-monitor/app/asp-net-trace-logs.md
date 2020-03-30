---
title: Utforska .NET-spårningsloggar i Application Insights
description: Sökloggar som genereras av Trace, NLog eller Log4Net.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276275"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Utforska spåra loggar för .NET/.NET Core och Python i Application Insights

Skicka diagnostikspårningsloggar för ditt ASP.NET/ASP.NET Core-program från ILogger, NLog, log4Net eller System.Diagnostics.Trace till [Azure Application Insights][start]. För Python-program skickar du diagnostikspårningsloggar med AzureLogHandler i OpenCensus Python för Azure Monitor. Du kan sedan utforska och söka efter dem. Dessa loggar sammanfogas med de andra loggfilerna från ditt program, så att du kan identifiera spårningar som är associerade med varje användarbegäran och korrelera dem med andra händelser och undantagsrapporter.

> [!NOTE]
> Behöver du logg-capture-modulen? Det är en användbar adapter för tredjepartsloggers. Men om du inte redan använder NLog, log4Net eller System.Diagnostics.Trace kan du överväga att bara anropa [**Application Insights TrackTrace()**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) direkt.
>
>
## <a name="install-logging-on-your-app"></a>Installera loggning på din app
Installera ditt valda loggningsramverk i projektet, vilket bör resultera i en post i app.config eller web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Konfigurera programstatistik för att samla in loggar
[Lägg till programstatistik i projektet](../../azure-monitor/app/asp-net.md) om du inte har gjort det ännu. Du ser ett alternativ för att inkludera logginsamlaren.

Eller högerklicka på projektet i Solution Explorer för att **konfigurera programstatistik**. Välj alternativet **Konfigurera spårningssamling.**

> [!NOTE]
> Ingen application insights-meny eller logginsamlare alternativ? Prova [felsökning](#troubleshooting).

## <a name="manual-installation"></a>Manuell installation
Använd den här metoden om projekttypen inte stöds av installationsprogrammet för Application Insights (till exempel ett Windows-skrivbordsprojekt).

1. Om du planerar att använda log4Net eller NLog installerar du det i projektet.
2. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**.
3. Sök efter "Application Insights".
4. Välj något av följande paket:

   - För ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - För NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - För Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - För System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet-paketet installerar nödvändiga sammansättningar och ändrar web.config eller app.config om det är tillämpligt.

## <a name="ilogger"></a>ILogger

Exempel på hur du använder Application Insights ILogger-implementeringen med konsolprogram och ASP.NET Core finns i [ApplicationInsightsLoggerProvider för .NET Core ILogger-loggar](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Infoga diagnostiska logganrop
Om du använder System.Diagnostics.Trace är ett vanligt anrop:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Om du föredrar log4net eller NLog använder du:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Använda EventSource-händelser
Du kan konfigurera [System.Diagnostics.Tracing.EventSource-händelser](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) som ska skickas till Application Insights som spårningar. Installera först `Microsoft.ApplicationInsights.EventSourceListener` NuGet-paketet. Redigera sedan `TelemetryModules` avsnittet [i filen ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa kan du ställa in följande parametrar:
 * **Namn** anger namnet på den EventSource som ska samlas in.
 * **Nivå** anger loggningsnivån för insamling: *Kritisk*, *Fel*, *Informations-,* *LogAlways,* *Utförlig*eller *Varning*.
 * **Nyckelord** (valfritt) anger heltalsvärdet för nyckelordskombinationer som ska användas.

## <a name="use-diagnosticsource-events"></a>Använda diagnostikkällor
Du kan konfigurera [System.Diagnostics.DiagnosticSource-händelser](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) som ska skickas till Application Insights som spårningar. Installera först [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet-paketet. Redigera sedan avsnittet "TelemetriModules" i filen [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

För varje DiagnosticSource som du vill spåra lägger du till en post med **attributet Name** set till namnet på din DiagnosticSource.

## <a name="use-etw-events"></a>Använda ETW-händelser
Du kan konfigurera ETW-händelser (Event Tracing for Windows) som ska skickas till Application Insights som spårningar. Installera först `Microsoft.ApplicationInsights.EtwCollector` NuGet-paketet. Redigera sedan avsnittet "TelemetriModules" i filen [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

> [!NOTE] 
> ETW-händelser kan bara samlas in om processen som är värd för SDK körs under en identitet som är medlem i prestandalogganvändare eller administratörer.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa kan du ställa in följande parametrar:
 * **ProviderName** är namnet på ETW-providern att samla in.
 * **ProviderGuid** anger GUID för ETW-providern att samla in. Den kan användas `ProviderName`i stället för .
 * **Nivå** ställer in loggningsnivån för insamling. Det kan vara *kritiskt*, *fel*, *information,* *logalways*, *utförlig*eller *varning*.
 * **Nyckelord** (valfritt) anger heltalsvärdet för sökordskombinationer som ska användas.

## <a name="use-the-trace-api-directly"></a>Använda Spårnings-API:et direkt
Du kan anropa programmet för spårning av programinsikter direkt. Loggningskorten använder det här API:et.

Ett exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

En fördel med TrackTrace är att du kan lägga relativt långa data i meddelandet. Du kan till exempel koda POST-data där.

Du kan också lägga till en allvarlighetsgrad i meddelandet. Och precis som annan telemetri kan du lägga till egenskapsvärden som hjälper till att filtrera eller söka efter olika uppsättningar spårningar. Ett exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

På så sätt kan du enkelt filtrera bort [alla][diagnostic] meddelanden på en viss allvarlighetsgrad som relaterar till en viss databas.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler för OpenCensus Python
Med Azure Monitor Log Handler kan du exportera Python-loggar till Azure Monitor.

Instrumentera ditt program med [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) för Azure Monitor.

Det här exemplet visar hur du skickar en varningsnivålogg till Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Utforska dina loggar
Kör appen i felsökningsläge eller distribuera den live.

Välj [Sök][diagnostic]i appens [översiktsfönster i portalen Application Insights][portal].

Du kan till exempel:

* Filtrera på loggspårningar eller på objekt med specifika egenskaper.
* Kontrollera ett visst objekt i detalj.
* Hitta andra systemloggdata som relaterar till samma användarbegäran (har samma OperationId).
* Spara konfigurationen av en sida som en favorit.

> [!NOTE]
>Om ditt program skickar mycket data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare kan *funktionen för adaptiv sampling* fungera och skicka endast en del av din telemetri. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Felsökning
### <a name="how-do-i-do-this-for-java"></a>Hur gör jag detta för Java?
Använd [Java-loggadaptrarna](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Det finns inget alternativ för programinsikter på projektets snabbmeny
* Kontrollera att Developer Analytics Tools är installerat på utvecklingsmaskinen. På Tillägg och uppdateringar för Visual **Studio-verktyg** > **Extensions and Updates**letar du efter **utvecklaranalysverktyg**. Om den inte finns på fliken **Installerad** öppnar du fliken **Online** och installerar den.
* Det kan vara en projekttyp som Devloper Analytics Tools inte stöder. Använd [manuell installation](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Det finns inget alternativ för loggadapter i konfigurationsverktyget
* Installera loggningsramverket först.
* Om du använder System.Diagnostics.Trace kontrollerar du att du har konfigurerat det [på *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Kontrollera att du har den senaste versionen av Application Insights. Gå till > **Verktygstillägg och uppdateringar i**Visual Studio och öppna fliken **Tools** **Uppdateringar.** Om **Developer Analytics Tools** finns där väljer du den för att uppdatera den.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Felmeddelandet "Instrumenteringsnyckeln kan inte vara tom" visas i felmeddelandet "Instrumenteringsnyckeln"
Du har förmodligen installerat loggningskortet Nuget-paketet utan att installera Application Insights. Högerklicka på *ApplicationInsights.config*i Solution Explorer och välj **Uppdatera programstatistik**. Du uppmanas att logga in på Azure och skapa en Application Insights-resurs eller återanvända en befintlig. Det borde lösa problemet.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Jag kan se spår men inte andra händelser i diagnostisk sökning
Det kan ta ett tag för alla händelser och förfrågningar att komma igenom pipelinen.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Hur mycket data lagras?
Flera faktorer påverkar mängden data som behålls. Mer information finns i [avsnittet gränser](../../azure-monitor/app/api-custom-events-metrics.md#limits) på sidan kundhändelsemått.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Jag ser inga loggposter som jag förväntade mig
Om ditt program skickar omfattande mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare, kan funktionen för adaptiv sampling fungera och skicka endast en del av din telemetri. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>Nästa steg

* [Diagnostisera fel och undantag i ASP.NET][exceptions]
* [Läs mer om Sök][diagnostic]
* [Ställa in tillgänglighets- och svarstiderstester][availability]
* [Troubleshooting][qna] (Felsökning)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
