---
title: Utforska .NET-spårnings loggar i Application Insights
description: Sök efter loggar som genereras av trace, NLog eller Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: 654e4bc35de1ed33842944ba360d319705589683
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372512"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Utforska loggarna .NET/.NET Core och python i Application Insights

Skicka diagnostiska spårnings loggar för ditt ASP.NET/ASP.NET Core-program från ILogger, NLog, log4Net eller system. Diagnostics. trace till [Azure Application insikter][start]. För python-program skickar du diagnostiska spårnings loggar med AzureLogHandler i openräkningar python för Azure Monitor. Du kan sedan utforska och söka i dem. Dessa loggar slås samman med de andra loggfilerna från ditt program, så att du kan identifiera spårningar som är associerade med varje Användarbegäran och korrelera dem med andra händelser och undantags rapporter.

> [!NOTE]
> Behöver du modulen för logg avbildning? Det är ett användbart kort för loggar från tredje part. Men om du inte redan använder NLog, log4Net eller system. Diagnostics. trace bör du överväga att bara anropa [**Application Insights TrackTrace ()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) direkt.
>
>
## <a name="install-logging-on-your-app"></a>Installera loggning i din app
Installera det valda loggnings ramverket i projektet, vilket bör resultera i en post i app. config eller Web. config.

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

## <a name="configure-application-insights-to-collect-logs"></a>Konfigurera Application Insights för att samla in loggar
[Lägg till Application Insights i projektet](../../azure-monitor/app/asp-net.md) om du inte har gjort det än. Du ser ett alternativ för att inkludera logg insamlaren.

Eller högerklicka på ditt projekt i Solution Explorer för att **konfigurera Application Insights**. Välj alternativet **Konfigurera spårnings samling** .

> [!NOTE]
> Ingen Application Insights meny eller logg insamlings alternativ? Försök [Felsöka](#troubleshooting).

## <a name="manual-installation"></a>Manuell installation
Använd den här metoden om din projekt typ inte stöds av installations programmet för Application Insights (till exempel ett Windows Desktop-projekt).

1. Om du planerar att använda log4Net eller NLog kan du installera det i projektet.
2. I Solution Explorer högerklickar du på projektet och väljer **Hantera NuGet-paket**.
3. Sök efter "Application Insights".
4. Välj något av följande paket:

   - För ILogger: [Microsoft. Extensions. logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - För NLog: [Microsoft. ApplicationInsights. NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - För Log4Net: [Microsoft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - För system. Diagnostics: [Microsoft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft. ApplicationInsights. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet-paketet installerar nödvändiga sammansättningar och ändrar Web. config eller app. config om det är tillämpligt.

## <a name="ilogger"></a>ILogger

Exempel på hur du använder Application Insights ILogger-implementering med konsol program och ASP.NET Core finns i [ApplicationInsightsLoggerProvider för .net Core ILogger-loggar](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Infoga diagnostiska logg anrop
Om du använder system. Diagnostics. trace är ett typiskt anrop att:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Om du föredrar log4net eller NLog använder du:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Använda EventSource-händelser
Du kan konfigurera [system. Diagnostics. tracing. EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) -händelser som ska skickas till Application Insights som spår. Installera först `Microsoft.ApplicationInsights.EventSourceListener` NuGet-paketet. Redigera sedan `TelemetryModules`-avsnittet i filen [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa kan du ange följande parametrar:
 * **Namn** anger namnet på den EventSource som ska samlas in.
 * **Nivå** anger den loggnings nivå som ska samlas in: *kritisk*, *fel*, *information*, *LogAlways*, *utförlig*eller *Varning*.
 * **Nyckelord** (valfritt) Ange heltal svärdet för nyckelords kombinationer som ska användas.

## <a name="use-diagnosticsource-events"></a>Använda DiagnosticSource-händelser
Du kan konfigurera [system. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) -händelser som ska skickas till Application Insights som spår. Installera först [`Microsoft.ApplicationInsights.DiagnosticSourceListener` NuGet-](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) paketet. Redigera sedan avsnittet "TelemetryModules" i filen [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

För varje DiagnosticSource som du vill spåra lägger du till en post med attributet **Name** angivet till namnet på din DiagnosticSource.

## <a name="use-etw-events"></a>Använda ETW-händelser
Du kan konfigurera ETW (Event Tracing for Windows)-händelser (ETW) som ska skickas till Application Insights som spår. Installera först `Microsoft.ApplicationInsights.EtwCollector` NuGet-paketet. Redigera sedan avsnittet "TelemetryModules" i filen [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

> [!NOTE] 
> ETW-händelser kan bara samlas in om processen som är värd för SDK: n körs under en identitet som är medlem i prestanda loggar användare eller administratörer.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa kan du ange följande parametrar:
 * **ProviderName** är namnet på ETW-providern som ska samlas in.
 * **ProviderGuid** anger GUID för ETW-providern som ska samlas in. Den kan användas i stället för `ProviderName`.
 * **Nivå** anger den loggnings nivå som ska samlas in. Det kan vara *kritiskt*, *fel*, *information*, *LogAlways*, *utförlig*eller *Varning*.
 * **Nyckelord** (valfritt) Ange heltal svärdet för nyckelords kombinationer som ska användas.

## <a name="use-the-trace-api-directly"></a>Använda trace API direkt
Du kan anropa API: et för Application Insights trace direkt. Loggnings korten använder detta API.

Exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

En fördel med TrackTrace är att du kan ställa in relativt långa data i meddelandet. Du kan till exempel koda POST-data där.

Du kan också lägga till en allvarlighets grad i meddelandet. Liksom annan telemetri kan du lägga till egenskaps värden för att filtrera eller söka efter olika uppsättningar med spår. Exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

På så sätt kan du enkelt filtrera ut i [söka][diagnostic] alla meddelanden med en viss allvarlighets grad som relaterar till en viss databas.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler för openräkningar python
Med Azure Monitor logg hanteraren kan du exportera python-loggar till Azure Monitor.

Instrumentera ditt program med [python-SDK: n för openräkning](../../azure-monitor/app/opencensus-python.md) för Azure Monitor.

I det här exemplet visas hur du skickar en varnings nivå logg till Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Utforska dina loggar
Kör appen i fel söknings läge eller distribuera den Live.

I appens översikts fönster i [Application Insights-portalen][portal]väljer du [Sök][diagnostic].

Du kan till exempel:

* Filtrera efter logg spår eller objekt med vissa egenskaper.
* Granska ett särskilt objekt i detalj.
* Hitta andra system logg data som relaterar till samma Användarbegäran (har samma OperationId).
* Spara konfigurationen av en sida som en favorit.

> [!NOTE]
>Om ditt program skickar mycket data och du använder Application Insights SDK för ASP.NET-version 2.0.0-beta3 eller senare, kan den *anpassningsbara samplings* funktionen hantera och bara skicka en del av din telemetri. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Felsöka
### <a name="how-do-i-do-this-for-java"></a>Hur gör jag för att gör du detta för Java?
Använd [Java log-nätverkskorten](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Det finns inget Application Insights alternativ på snabb menyn i Project
* Se till att Developer Analytics Tools är installerat på utvecklings datorn. I Visual Studio **Tools** > **tillägg och uppdateringar**kan du leta efter **Developer Analytics-verktyg**. Om den inte finns på fliken **installerad** öppnar du fliken **online** och installerar den.
* Det kan vara en projekt typ som Devloper analys verktyg inte stöder. Använd [manuell installation](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Det finns inget logg korts alternativ i konfigurations verktyget
* Installera loggnings ramverket först.
* Om du använder system. Diagnostics. trace kontrollerar du att du har [konfigurerat den i *Web. config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Kontrol lera att du har den senaste versionen av Application Insights. I Visual Studio går du till **verktyg** > **tillägg och uppdateringar**och öppnar fliken **uppdateringar** . Om **Developer Analytics tools** finns där väljer du det för att uppdatera det.

### <a name="emptykey"></a>Jag får fel meddelandet "Instrumentation-nyckeln kan inte vara tom"
Du har förmodligen installerat NuGet-paketet för loggnings kortet utan att installera Application Insights. I Solution Explorer högerklickar du på *ApplicationInsights. config*och väljer **Uppdatera Application Insights**. Du uppmanas att logga in på Azure och skapa en Application Insights resurs eller återanvända en befintlig. Det bör åtgärda problemet.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Jag kan se spårningar men inte andra händelser i diagnostisk sökning
Det kan ta en stund innan alla händelser och begär Anden kan gå igenom pipelinen.

### <a name="limits"></a>Hur mycket data behålls?
Flera faktorer påverkar mängden data som behålls. Mer information finns i avsnittet om [begränsningar](../../azure-monitor/app/api-custom-events-metrics.md#limits) på sidan för kundens händelse mått.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Jag ser inte några logg poster som jag förväntade mig
Om ditt program skickar Voluminous data mängder och du använder Application Insights SDK för ASP.NET-version 2.0.0-beta3 eller senare, kan den anpassningsbara samplings funktionen hantera och bara skicka en del av din telemetri. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Nästa steg

* [Diagnostisera fel och undantag i ASP.NET][exceptions]
* [Läs mer om sökning][diagnostic]
* [Ställ in tillgänglighets-och svars tider][availability]
* [Troubleshooting][qna] (Felsökning)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md