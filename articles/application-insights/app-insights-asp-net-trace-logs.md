---
title: Utforska .NET spårningsloggarna i Application Insights
description: Sök loggar som genereras med spårning, NLog och Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 5ffb758fe5fa42be6323de06afbfb38068ae1926
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969178"
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Utforska .NET spårningsloggarna i Application Insights
Om du använder NLog, log4Net eller System.Diagnostics.Trace för diagnostikspårning i ASP.NET-program, kan du använda dina loggar som skickas till [Azure Application Insights][start], där du kan utforska och söka dem. Loggarna tillsammans med den telemetri som kommer från ditt program, så att du kan identifiera spårningar som är associerade med Underhåll varje användarbegäran och korrelera dem med andra händelser och undantagsrapporter.

> [!NOTE]
> Behöver du avbilda-modulen programlogg? Det är en användbar adapter för 3 part tangenttryckningar, men om du inte redan använder NLog, log4Net eller System.Diagnostics.Trace, bör du bara anropa [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) direkt.
>
>

## <a name="install-logging-on-your-app"></a>Installera loggning i din app
Installera din valda loggningsramverk i projektet. Detta resulterar i en post i app.config eller web.config.

Om du använder System.Diagnostics.Trace, måste du lägga till en post i web.config:

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>Konfigurera Application Insights för att samla in loggar
**[Lägg till Application Insights i projektet](app-insights-asp-net.md)**  om du inte har gjort det ännu. Ser du ett alternativ för att inkludera logginsamlaren.

Eller **konfigurera Application Insights** genom att högerklicka på projektet i Solution Explorer. Välj alternativet för att **konfigurera spårningsinsamling**.

*Inga menyn eller log insamlaren alternativ för Application Insights?* Försök [felsökning](#troubleshooting).

## <a name="manual-installation"></a>Manuell installation
Använd den här metoden om din projekttyp inte stöds av Application Insights-installationsprogrammet (till exempel ett Windows desktop-projekt).

1. Om du planerar att använda log4Net eller NLog, kan du installera det i projektet.
2. Högerklicka på projektet i Solution Explorer och välj **hantera NuGet-paket**.
3. Sök efter ”Application Insights”
4. Välj lämpligt paket - en av:

   * Microsoft.ApplicationInsights.TraceListener (för att spela in System.Diagnostics.Trace anrop)
   * Microsoft.ApplicationInsights.EventSourceListener (för att samla in EventSource händelser)
   * Microsoft.ApplicationInsights.EtwListener (för att samla in ETW-händelser)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

NuGet-paketet installerar nödvändiga sammansättningar och ändrar också web.config eller app.config.

## <a name="insert-diagnostic-log-calls"></a>Infoga diagnostiklogg anrop
Om du använder System.Diagnostics.Trace, blir en typisk anrop:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Om du föredrar log4net eller NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Med hjälp av EventSource händelser
Du kan konfigurera [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) händelser som ska skickas till Application Insights som spårningar. Installera först den `Microsoft.ApplicationInsights.EventSourceListener` NuGet-paketet. Redigera `TelemetryModules` delen av den [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) fil.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa, kan du ange följande parametrar:
 * `Name` Anger namnet på händelsekällan att samla in.
 * `Level` Anger loggningsnivån att samla in. Kan vara något av `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Valfritt) anger heltalsvärdet för nyckelord kombinationer för att använda.

## <a name="using-diagnosticsource-events"></a>Med hjälp av DiagnosticSource händelser
Du kan konfigurera [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) händelser som ska skickas till Application Insights som spårningar. Installera först den [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet-paketet. Redigera den `TelemetryModules` delen av den [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) fil.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

För varje DiagnosticSource som du vill spåra, lägger du till en post med den `Name` attributet inställt på namnet på din DiagnosticSource.

## <a name="using-etw-events"></a>Med hjälp av ETW-händelser
Du kan konfigurera ETW-händelser som ska skickas till Application Insights som spårningar. Installera först den `Microsoft.ApplicationInsights.EtwCollector` NuGet-paketet. Redigera `TelemetryModules` delen av den [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) fil.

> [!NOTE] 
> ETW-händelser kan endast samlas in om processen som är värd för SDK körs under en identitet som är medlem i ”Performance Log Users” eller administratörer.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

För varje källa, kan du ange följande parametrar:
 * `ProviderName` är namnet på ETW-provider för att samla in.
 * `ProviderGuid` Anger GUID för ETW-provider för att samla in, kan användas i stället för `ProviderName`.
 * `Level` Anger loggningsnivån att samla in. Kan vara något av `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Valfritt) anger heltalsvärdet för nyckelordet kombinationer för att använda.

## <a name="using-the-trace-api-directly"></a>Med hjälp av spårningen API direkt
Du kan anropa API för Application Insights-spårningen direkt. Loggning korten använda detta API.

Exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

En fördel med TrackTrace är att du kan publicera relativt lång data i meddelandet. Du kan exempelvis koda det postdata.

Dessutom kan du lägga till en allvarlighetsgrad ditt meddelande. Och precis som andra telemetri, du kan lägga till egenskapsvärden som du kan använda för att filtrera eller Sök efter olika uppsättningar av spår. Exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Detta gör att du, i [Search][diagnostic], för att enkelt filtrera bort alla meddelanden som tillhör en viss allvarlighetsgrad som rör en viss databas.

## <a name="explore-your-logs"></a>Utforska dina loggar
Kör din app, antingen i felsökningsläge eller distribuera den live.

I din app-översiktsbladet [Application Insights-portalen][portal], Välj [Search][diagnostic].

![Välj sökning i Application Insights](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Search](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Du kan till exempel:

* Filtrera efter loggspårningar eller efter objekt med specifika egenskaper
* Granska ett specifikt objekt i detalj.
* Hitta andra telemetri som är relaterade till samma användarbegäran (det vill säga med samma OperationId)
* Spara konfigurationen av den här sidan som en favorit

> [!NOTE]
> **Sampling.** Om programmet skickar stora mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare kan den anpassade samplingsfunktionen skicka bara en del av din telemetri. [Läs mer om sampling.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>Nästa steg
[Diagnostisera fel och undantag i ASP.NET][exceptions]

[Läs mer om Search][diagnostic].

## <a name="troubleshooting"></a>Felsökning
### <a name="how-do-i-do-this-for-java"></a>Hur ska jag göra detta för Java?
Använd den [Java log kort](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Det finns inget alternativ för Application Insights på snabbmenyn för projektet
* Kontrollera att Application Insights tools är installerat på den här utvecklingsdator. Leta efter Application Insights Tools i Visual Studio-menyn Verktyg, tillägg och uppdateringar. Om det inte finns i fliken installerad, öppna fliken Online och installera den.
* Det kan vara en typ av projekt som inte stöds av Application Insights-verktygen. Använd [manuell installation](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Inget alternativ för log-nätverkskort i konfigurationsverktyget
* Du måste först installera vilket loggningsramverk.
* Om du använder System.Diagnostics.Trace, se till att du [har konfigurerat detta i `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Har du har den senaste versionen av Application Insights? I Visual Studio **verktyg** menyn, Välj **tillägg och uppdateringar**, och öppna den **uppdateringar** fliken. Om Developer Analytics tools finns där, klicka för att uppdatera den.

### <a name="emptykey"></a>Det uppstår ett fel ”instrumenteringsnyckeln kan inte vara tom”
Det verkar som du har installerat Nuget-paketet loggning nätverkskort utan att installera Application Insights.

I Solution Explorer högerklickar du på `ApplicationInsights.config` och välj **Update Application Insights**. Du får en dialogruta där du kan logga in på Azure och skapa en Application Insights-resurs eller återanvända en befintlig. Som bör åtgärdas.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Jag kan se spårningarna i diagnostiksökning, men inte de andra händelserna
Det kan ibland ta en stund innan alla händelser och förfrågningar om att hämta genom pipelinen.

### <a name="limits"></a>Hur mycket data finns kvar?
Flera faktorer som påverkar mängden data som kvarhålls. Se den [gränser](app-insights-api-custom-events-metrics.md#limits) delen av sidan kund händelse mått för mer information. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Jag ser inte några av de loggposter som jag förväntar mig
Om programmet skickar stora mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare kan den anpassade samplingsfunktionen skicka bara en del av din telemetri. [Läs mer om sampling.](app-insights-sampling.md)

## <a name="add"></a>Nästa steg
* [Konfigurera tillgänglighet och svarstider tester][availability]
* [Felsökning][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
