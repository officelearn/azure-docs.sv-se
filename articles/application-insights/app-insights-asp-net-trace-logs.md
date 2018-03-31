---
title: Utforska .NET spårningsloggar i Application Insights
description: Söka i loggar som genereras med spårning, NLog och Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 574b11f9ba38bda775610f2f9e90fbb2d2b05868
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Utforska .NET spårningsloggar i Application Insights
Om du använder NLog, log4Net eller System.Diagnostics.Trace för diagnostikspårning i ASP.NET-program du har dina loggar som skickas till [Azure Application Insights][start], där du kan utforska och söka efter dem. Loggarna sammanfogas med andra telemetri som kommer från ditt program så att du kan identifiera spåren som är associerade med varje användarbegäran servicing och korrelera dem med andra händelser och undantag rapporter.

> [!NOTE]
> Behöver du modulen loggen avbildning? Det är ett nätverkskort som är användbar för 3 parts loggare, men om du inte redan använder NLog, log4Net eller System.Diagnostics.Trace, bör du bara anropa [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) direkt.
>
>

## <a name="install-logging-on-your-app"></a>Installera loggning på din app
Installera din valda loggningsramverk i projektet. Det bör resultera i en post i app.config eller web.config.

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
**[Lägg till Application Insights i ditt projekt](app-insights-asp-net.md)**  om du inte har gjort det ännu. Välja att inkludera logginsamlaren visas.

Eller **konfigurera Application Insights** genom att högerklicka på projektet i Solution Explorer. Välj alternativet för att **konfigurera insamling**.

*Inget Application Insights-menyn eller loggfil insamlaren alternativ?* Försök [felsökning](#troubleshooting).

## <a name="manual-installation"></a>Manuell installation
Använd den här metoden om dina projekttypen inte stöds av installationsprogrammet för Application Insights (till exempel en Windows desktop projekt).

1. Om du planerar att använda log4Net eller NLog, installera den i projektet.
2. Högerklicka på projektet i Solution Explorer och välj **hantera NuGet-paket**.
3. Sök efter ”Application Insights”
4. Välj lämpligt paket - en av:

   * Microsoft.ApplicationInsights.TraceListener (för att avbilda System.Diagnostics.Trace anrop)
   * Microsoft.ApplicationInsights.EventSourceListener (för att fånga EventSource händelser)
   * Microsoft.ApplicationInsights.EtwListener (för att avbilda ETW-händelser)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

NuGet-paketet installerar nödvändiga sammansättningar och ändrar också web.config eller app.config.

## <a name="insert-diagnostic-log-calls"></a>Infoga diagnostiska loggen anrop
Om du använder System.Diagnostics.Trace, är en typisk anropet:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Om du föredrar log4net eller NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Med hjälp av EventSource händelser
Du kan konfigurera [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) händelser skickas till Application Insights som spårningar. Installera först den `Microsoft.ApplicationInsights.EventSourceListener` NuGet-paketet. Redigera `TelemetryModules` avsnitt i den [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) fil.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

För varje datakälla kan du ange följande parametrar:
 * `Name` Anger namnet på EventSource att samla in.
 * `Level` Anger loggningsnivån att samla in. Kan vara något av `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Valfritt) anger nyckelord kombinationer för att använda heltalsvärde.

## <a name="using-diagnosticsource-events"></a>Med hjälp av DiagnosticSource händelser
Du kan konfigurera [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) händelser skickas till Application Insights som spårningar. Installera först den [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet-paketet. Redigera den `TelemetryModules` avsnitt i den [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) fil.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

För varje DiagnosticSource som du vill spåra lägger du till en post med den `Name` -attributet inställt på namnet på din DiagnosticSource.

## <a name="using-etw-events"></a>Med hjälp av ETW-händelser
Du kan konfigurera ETW-händelser som ska skickas till Application Insights som spårningar. Installera först den `Microsoft.ApplicationInsights.EtwCollector` NuGet-paketet. Redigera `TelemetryModules` avsnitt i den [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) fil.

> [!NOTE] 
> ETW-händelser kan samlas endast om processen som är värd SDK körs under en identitet som är medlem i ”användare” eller administratörer.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

För varje datakälla kan du ange följande parametrar:
 * `ProviderName` är namnet på ETW-provider för att samla in.
 * `ProviderGuid` Anger GUID för ETW-provider för att samla in, kan användas i stället för `ProviderName`.
 * `Level` Anger loggningsnivån att samla in. Kan vara något av `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Valfritt) anger nyckelordet kombinationer för att använda heltalsvärde.

## <a name="using-the-trace-api-directly"></a>Med hjälp av spårningen API direkt
Du kan anropa Application Insights trace API direkt. Loggning korten använder detta API.

Exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

En fördel med TrackTrace är att du kan publicera relativt lång data i meddelandet. Du kan till exempel kodar det postdata.

Dessutom kan du lägga till en allvarlighetsgrad för meddelandet. Och precis som andra telemetri kan du lägga till värden som du kan använda för att filtrera eller söka efter olika uppsättningar av spår. Exempel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Detta gör att du, i [Sök][diagnostic]enkelt filtrera bort meddelandena som en viss allvarlighetsgrad som rör en viss databas.

## <a name="explore-your-logs"></a>Utforska dina loggar
Kör appen i felsökningsläge eller distribuera den live.

I bladet för din app översikt i [Application Insights-portalen][portal], Välj [Sök][diagnostic].

![Välj sökning i Application Insights](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Search](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Du kan till exempel:

* Filtrera efter loggspårningar eller efter artiklar med specifika egenskaper
* Kontrollera om ett specifikt objekt i detalj.
* Hitta andra telemetri som relaterar till samma användarbegäran (det vill säga med samma åtgärds-ID)
* Spara konfigurationen av den här sidan som en favorit

> [!NOTE]
> **Sampling.** Om programmet skickar stora mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare kan den anpassade samplingsfunktionen skicka bara en del av din telemetri. [Läs mer om sampling.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>Nästa steg
[Diagnostisera fel och undantag i ASP.NET][exceptions]

[Mer information om sökningen][diagnostic].

## <a name="troubleshooting"></a>Felsökning
### <a name="how-do-i-do-this-for-java"></a>Hur gör jag för Java?
Använd den [Java loggen kort](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Det finns inget alternativ för Application Insights i projekt snabbmenyn
* Kontrollera att Application Insights tools är installerat på den här datorn för utveckling. Leta efter Application Insights Tools i Verktyg för Visual Studio-menyn, tillägg och uppdateringar. Om den inte på fliken installerad öppnar du fliken Online och installera den.
* Det kan vara en typ av projekt som inte stöds av Application Insights verktyg. Använd [manuell installation](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Inget loggen kortet alternativ i verktyget configuration
* Du måste först installera loggning ramen.
* Om du använder System.Diagnostics.Trace, se till att du [konfigurerats i `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Har du fått den senaste versionen av Application Insights? I Visual Studio **verktyg** -menyn, Välj **tillägg och uppdateringar**, och öppna den **uppdateringar** fliken. Om utvecklare Analytics verktyg det klickar du på om du vill uppdatera den.

### <a name="emptykey"></a>Ett felmeddelande ”Instrumentation nyckeln kan inte vara tomt”
Ser ut som om du har installerat Nuget-paketet loggning nätverkskortet utan att installera Application Insights.

I Solution Explorer högerklickar du på `ApplicationInsights.config` och välj **uppdatering Application Insights**. Du får en dialogruta som uppmanar dig att logga in på Azure och skapa en Application Insights-resurs eller återanvända en befintlig. Som ska åtgärdas.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Jag kan se spåren i diagnostiska sökning, men inte andra händelser
Det kan ta en stund för alla händelser och begäranden att hämta via pipeline.

### <a name="limits"></a>Hur mycket data finns kvar?
Flera faktorer påverka mängden data som behålls. Finns det [gränser](app-insights-api-custom-events-metrics.md#limits) avsnitt på sidan customer händelse mått för mer information. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Jag ser inte några av de loggposter som förväntat
Om programmet skickar stora mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare kan den anpassade samplingsfunktionen skicka bara en del av din telemetri. [Läs mer om sampling.](app-insights-sampling.md)

## <a name="add"></a>Nästa steg
* [Ställ in tillgänglighet och svarstider tester][availability]
* [Felsökning][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
