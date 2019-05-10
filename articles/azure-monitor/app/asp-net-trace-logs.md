---
title: Utforska .NET spårningsloggarna i Application Insights
description: Sök loggar som genereras av spårning, NLog och Log4Net.
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
ms.openlocfilehash: d366f363b7bd1d5306d598c9b38258eb78076b7c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472057"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Utforska.NET/.NET Core spårningsloggar i Application Insights

Skicka diagnostikspårning loggar för din ASP.NET/ASP.NET Core-App från ILogger, NLog, log4Net eller System.Diagnostics.Trace till [Azure Application Insights][start]. Du kan utforska och söka efter den. Dessa loggar slås samman med andra loggfiler från ditt program, så att du kan identifiera spårningar som är associerade med varje användarbegäran och korrelera dem med andra händelser och undantagsrapporter.

> [!NOTE]
> Behöver du modulen log-capture? Det är en användbar adapter för tredje parts tangenttryckningar. Men om du inte redan använder NLog, log4Net eller System.Diagnostics.Trace kan du bara anropa [ **Application Insights TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) direkt.
>
>
## <a name="install-logging-on-your-app"></a>Installera loggning i din app
Installera din valda loggningsramverk i projektet, vilket resulterar i en post i app.config eller web.config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Konfigurera Application Insights för att samla in loggar
[Lägg till Application Insights i projektet](../../azure-monitor/app/asp-net.md) om du inte har gjort det ännu. Ser du ett alternativ för att inkludera logginsamlaren.

Eller högerklicka på projektet i Solution Explorer **konfigurera Application Insights**. Välj den **konfigurera spårningsinsamling** alternativet.

> [!NOTE]
> Inga menyn eller log insamlaren alternativ för Application Insights? Försök [felsökning](#troubleshooting).

## <a name="manual-installation"></a>Manuell installation
Använd den här metoden om din projekttyp inte stöds av Application Insights-installationsprogrammet (till exempel ett Windows desktop-projekt).

1. Om du planerar att använda log4Net eller NLog, kan du installera det i projektet.
2. Högerklicka på projektet i Solution Explorer och välj **hantera NuGet-paket**.
3. Sök efter ”Application Insights”.
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

NuGet-paketet installerar nödvändiga sammansättningar och ändrar web.config eller app.config om det behövs.

## <a name="ilogger"></a>ILogger

Exempel på hur du använder Application Insights ILogger-implementering med konsolprogram och ASP.NET Core finns [ApplicationInsightsLoggerProvider för .NET Core ILogger loggar](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Infoga diagnostiklogg anrop
Om du använder System.Diagnostics.Trace, blir en typisk anrop:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Om du föredrar log4net eller NLog, använder du:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Använda EventSource händelser
Du kan konfigurera [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) händelser som ska skickas till Application Insights som spårningar. Installera först den `Microsoft.ApplicationInsights.EventSourceListener` NuGet-paketet. Redigera den `TelemetryModules` delen av den [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fil.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa, kan du ange följande parametrar:
 * **Namn på** anger namnet på händelsekällan att samla in.
 * **Nivå** anger loggningsnivån att samla in: *Kritiska*, *fel*, *endast i informationssyfte*, *LogAlways*, *utförlig*, eller *varning*.
 * **Nyckelord** (valfritt) Ange heltalsvärdet för nyckelordet kombinationer för att använda.

## <a name="use-diagnosticsource-events"></a>Använda DiagnosticSource händelser
Du kan konfigurera [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) händelser som ska skickas till Application Insights som spårningar. Installera först den [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet-paketet. Redigera avsnittet ”TelemetryModules” i den [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fil.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

För varje DiagnosticSource som du vill spåra, lägger du till en post med den **namn** attributet inställt på namnet på din DiagnosticSource.

## <a name="use-etw-events"></a>Använda ETW-händelser
Du kan konfigurera för Windows ETW (Event Tracing) händelser som ska skickas till Application Insights som spårningar. Installera först den `Microsoft.ApplicationInsights.EtwCollector` NuGet-paketet. Redigera avsnittet ”TelemetryModules” i den [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fil.

> [!NOTE] 
> ETW-händelser kan endast samlas in om processen som är värd för SDK körs under en identitet som tillhör användare av prestandaloggar eller administratörer.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa, kan du ange följande parametrar:
 * **ProviderName** är namnet på ETW-provider för att samla in.
 * **ProviderGuid** Anger GUID för ETW-provider för att samla in. Den kan användas i stället för `ProviderName`.
 * **Nivå** anger loggningsnivån att samla in. Det kan vara *kritisk*, *fel*, *information*, *LogAlways*, *utförlig*, eller *Varning*.
 * **Nyckelord** (valfritt) Ange heltalsvärdet för nyckelordet kombinationer för att använda.

## <a name="use-the-trace-api-directly"></a>Använda spårningen API direkt
Du kan anropa API för Application Insights-spårningen direkt. Loggning korten använda detta API.

Exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

En fördel med TrackTrace är att du kan publicera relativt lång data i meddelandet. Du kan exempelvis koda det postdata.

Du kan också lägga till en allvarlighetsgrad ditt meddelande. Och precis som andra telemetri, du kan lägga till egenskapsvärden för att hjälpa filter eller för olika uppsättningar av spår. Exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Detta gör att du kan enkelt filtrera ut i [Search] [ diagnostic] alla meddelanden från en viss allvarlighetsgrad som är relaterade till en viss databas.

## <a name="explore-your-logs"></a>Utforska dina loggar
Kör appen i felsökningsläge eller distribuera den live.

I översiktsfönstret för din app i [Application Insights-portalen][portal]väljer [Search][diagnostic].

Du kan till exempel:

* Filtrera på loggspårningar eller objekt med specifika egenskaper.
* Granska ett specifikt objekt i detalj.
* Hitta andra system loggdata som relaterar till samma användarbegäran (har samma åtgärds-ID).
* Spara konfigurationen av en sida som en favorit.

> [!NOTE]
>Om ditt program skickar stora mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare, den *Adaptiv sampling* funktionen kan användas och skicka bara en del av din telemetri. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Felsökning
### <a name="how-do-i-do-this-for-java"></a>Hur ska jag göra detta för Java?
Använd den [Java log kort](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Det finns inget alternativ för Application Insights på snabbmenyn för projektet
* Se till att Developer Analytics Tools är installerat på utvecklingsdator. I Visual Studio **verktyg** > **tillägg och uppdateringar**, leta efter **Developer Analytics Tools**. Om den inte är på den **installerad** fliken, öppna den **Online** fliken och installera den.
* Det kan vara en projekttyp som inte stöder Devloper analysverktyg. Använd [manuell installation](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Det finns inget log nätverkskort alternativ i konfigurationsverktyget
* Installera vilket loggningsramverk först.
* Om du använder System.Diagnostics.Trace, se till att du har den [konfigurerats i *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Se till att du har den senaste versionen av Application Insights. I Visual Studio går du till **verktyg** > **tillägg och uppdateringar**, och öppna den **uppdateringar** fliken. Om **Developer Analytics Tools** är det, Välj den för att uppdatera den.

### <a name="emptykey"></a>Jag får felmeddelandet ”instrumenteringsnyckeln kan inte vara tom”
Du installerat förmodligen Nuget-paketet loggning nätverkskort utan att installera Application Insights. I Solution Explorer högerklickar du på *ApplicationInsights.config*, och välj **Update Application Insights**. Du uppmanas att logga in på Azure och skapa en Application Insights-resurs eller återanvända en befintlig. Som ska rätta till problemet.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Jag kan se spårningar men inte andra händelser i diagnostiksökning
Det kan ta en stund innan alla händelser och förfrågningar om att hämta genom pipelinen.

### <a name="limits"></a>Hur mycket data finns kvar?
Flera faktorer påverkar mängden data som sparas. Mer information finns i den [gränser](../../azure-monitor/app/api-custom-events-metrics.md#limits) delen av sidan kund händelse mått.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Jag ser inte vissa loggposter som jag hade förväntat mig
Om ditt program skickar omfattande mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare, kan funktionen Adaptiv sampling fungerar och skicka bara en del av din telemetri. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Nästa steg

* [Diagnostisera fel och undantag i ASP.NET][exceptions]
* [Mer information om sökning][diagnostic]
* [Konfigurera tillgänglighet och svarstider tester][availability]
* [Felsökning][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md