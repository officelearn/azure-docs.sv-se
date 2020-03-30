---
title: Diagnostisera med Live Metrics Stream - Azure Application Insights
description: Övervaka webbappen i realtid med anpassade mått och diagnostisera problem med en live-feed med fel, spårningar och händelser.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: ea0d786d0b8b96941d791bcc8e92fad9a869c5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670108"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Övervaka & diagnos med 1-sekunders latens

Undersök hjärtat i ditt live-, i-produktion webbapplikation med hjälp av Live Metrics Stream från [Application Insights](../../azure-monitor/app/app-insights-overview.md). Välj och filtrera mått och prestandaräknare att titta på i realtid, utan att störa tjänsten. Kontrollera stackspårningar från misslyckade begäranden och undantag från exempel. Tillsammans med [Profiler](../../azure-monitor/app/profiler.md), [Snapshot debugger](../../azure-monitor/app/snapshot-debugger.md). Live Metrics Stream är ett kraftfullt och icke-invasivt diagnostiskt verktyg för din livewebbplats.

Med Live Metrics Stream kan du:

* Validera en korrigering medan den släpps genom att titta på prestanda och fel räknas.
* Titta på effekten av testbelastningar och diagnostisera problem live.
* Fokusera på särskilda testsessioner eller filtrera bort kända problem genom att välja och filtrera de mått du vill titta på.
* Få undantagsspårningar när de inträffar.
* Experimentera med filter för att hitta de mest relevanta KPI:erna.
* Övervaka alla prestandaräknare för Windows live.
* Identifiera enkelt en server som har problem och filtrera all KPI/live-feed till just den servern.

[![Live Metrics Stream video](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Live-mått stöds för närvarande för ASP.NET, ASP.NET Core-, Azure-funktioner, Java och Node.js-appar.

## <a name="get-started"></a>Komma igång

1. Om du ännu inte har [installerat Application Insights](../../azure-monitor/azure-monitor-app-hub.yml) i din webbapp gör du det nu.
2. Utöver standardpaketen för Application Insights krävs [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) för att aktivera livestatistikström.
3. **Uppdatera till den senaste versionen** av Application Insights-paketet. Högerklicka på projektet i Visual Studio och välj **Hantera Nuget-paket.** Öppna fliken **Uppdateringar** och välj alla Microsoft.ApplicationInsights.* paket.

    Distribuera om din app.

3. Öppna application insights-resursen för din app i [Azure-portalen](https://portal.azure.com)och öppna sedan Live Stream.

4. [Skydda kontrollkanalen](#secure-the-control-channel) om du kan använda känsliga data, till exempel kundnamn i dina filter.

### <a name="no-data-check-your-server-firewall"></a>Ser du inga data? Kontrollera serverbrandväggen

Kontrollera att de [utgående portarna för Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) är öppna i brandväggen på dina servrar.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Hur skiljer sig Live Metrics Stream från Metrics Explorer och Analytics?

| |Direktsänd ström | Statistik Explorer och Analys |
|---|---|---|
|Svarstid|Data som visas inom en sekund|Aggregerat över minuter|
|Ingen kvarhållning|Data kvarstår medan de finns i diagrammet och ignoreras sedan|[Data som lagrats i 90 dagar](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|På begäran|Data strömmas medan du öppnar Live Metrics|Data skickas när SDK installeras och aktiveras|
|Kostnadsfri|Det kostar ingen avgift för Live Stream-data|Med förbehåll för [prissättning](../../azure-monitor/app/pricing.md)
|Samling|Alla valda mått och räknare överförs. Fel och stackspårningar samplas. TelemetriProcessorer används inte.|Händelser kan [provtas](../../azure-monitor/app/api-filtering-sampling.md)|
|Styrkanal|Filterkontrollsignaler skickas till SDK. Vi rekommenderar att du säkrar den här kanalen.|Kommunikation är ett sätt, till portalen|

## <a name="select-and-filter-your-metrics"></a>Markera och filtrera dina mått

(Tillgänglig med ASP.NET, ASP.NET Core och Azure Functions (v2).)

Du kan övervaka anpassad KPI live genom att använda godtyckliga filter på valfri Application Insights-telemetri från portalen. Klicka på filterkontrollen som visas när du muspekaren över något av diagrammen. Följande diagram ritar en anpassad KPI för antal begäranden med filter på attribut för URL och Varaktighet. Validera dina filter med avsnittet Förhandsversion i Stream som visar en live-feed av telemetri som matchar de villkor som du har angett när som helst.

![KPI för anpassad begäran](./media/live-stream/live-stream-filteredMetric.png)

Du kan övervaka ett annat värde än Antal. Alternativen beror på vilken typ av dataström som helst, vilket kan vara valfri Programinsiktstelemetri: begäranden, beroenden, undantag, spårningar, händelser eller mått. Det kan vara din egen [anpassade mätning:](../../azure-monitor/app/api-custom-events-metrics.md#properties)

![Värdealternativ](./media/live-stream/live-stream-valueoptions.png)

Förutom application insights telemetri kan du också övervaka alla Windows-prestandaräknare genom att välja det från strömalternativen och ange namnet på prestandaräknaren.

Live-mått aggregeras på två punkter: lokalt på varje server och sedan på alla servrar. Du kan ändra standardinställningen antingen genom att välja andra alternativ i respektive listruta.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Exempel på telemetri: Anpassade livediagnostiska händelser
Som standard visar direktflödet av händelser exempel på misslyckade begäranden och beroendeanrop, undantag, händelser och spårningar. Klicka på filterikonen om du vill visa de tillämpade villkoren när som helst. 

![Standard direktflöde](./media/live-stream/live-stream-eventsdefault.png)

Precis som med mått kan du ange eventuella godtyckliga kriterier för någon av telemetrityperna Application Insights. I det här exemplet väljer vi specifika begärandefel, spårningar och händelser. Vi väljer också alla undantag och beroendefel.

![Anpassad live-feed](./media/live-stream/live-stream-events.png)

För närvarande använder du det yttersta undantagsmeddelandet för meddelandebaserade villkor för undantag. I föregående exempel, för att filtrera bort godartade undantag med inre undantag meddelande (följer "< --" avgränsare) "Klienten frånkopplad." använder ett meddelande som inte innehåller villkor för felläsning av begärandeinnehåll.

Se information om ett objekt i liveflödet genom att klicka på det. Du kan pausa flödet antingen genom att klicka på **Paus** eller bara rulla nedåt eller klicka på ett objekt. Live-flödet återupptas när du rullar tillbaka till toppen, eller genom att klicka på räknaren med objekt som samlats in medan den pausades.

![Exempel på livefel](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrera efter serverinstans

Om du vill övervaka en viss serverrollinstans kan du filtrera efter server.

![Exempel på livefel](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>Säkra kontrollkanalen
De anpassade filterkriterier som du anger skickas tillbaka till komponenten Live Metrics i SDK för programinsikter. Filtren kan eventuellt innehålla känslig information, till exempel kund-ID: er. Du kan göra kanalen säker med en hemlig API-nyckel utöver instrumenteringsnyckeln.
### <a name="create-an-api-key"></a>Skapa en API-nyckel

![Skapa API-nyckel](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Lägga till API-nyckel i konfigurationen

### <a name="classic-aspnet"></a>Klassiska ASP.NET

I filen applicationinsights.config lägger du till AuthenticationApiKey i QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Eller i kod, ställ in den på QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Azure-funktionsappar

För Azure Function Apps (v2) kan det åstadkommas att skydda kanalen med en API-nyckel med en miljövariabel.

Skapa en API-nyckel inifrån application insights-resursen och gå till **Programinställningar för** din funktionsapp. Välj **Lägg till ny** inställning `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` och ange ett namn på och ett värde som motsvarar din API-nyckel.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (kräver application insights ASP.NET Core SDK 2.3.0 eller senare)

Ändra din startup.cs fil enligt följande:

Lägg först till

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Lägg sedan till i metoden ConfigureServices:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Men om du känner igen och litar på alla anslutna servrar kan du prova anpassade filter utan den autentiserade kanalen. Det här alternativet är tillgängligt i sex månader. Den här åsidosättningen krävs en gång varje ny session eller när en ny server är online.

![Alternativ för Live Metrics Auth](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Vi rekommenderar starkt att du ställer in den autentiserade kanalen innan du anger potentiellt känslig information som CustomerID i filtervillkoren.
>

## <a name="supported-features-table"></a>Tabell med funktioner som stöds

| Språk                         | Grundläggande mått       | Prestandamått | Anpassad filtrering    | Exempel på telemetri    | CPU delning efter process |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Stöds (V2.7.2+) | Stöds (V2.7.2+) | Stöds (V2.7.2+) | Stöds (V2.7.2+) | Stöds (V2.7.2+)  |
| .NET-kärna (target=.NET-ramverket)| Stöds (V2.4.1+) | Stöds (V2.4.1+) | Stöds (V2.4.1+) | Stöds (V2.4.1+) | Stöds (V2.4.1+)  |
| .NET-kärna (target=.NET-kärna)     | Stöds (V2.4.1+) | Stöds*          | Stöds (V2.4.1+) | Stöds (V2.4.1+) | **Stöds inte**    |
| Azure-funktioner v2               | Stöds           | Stöds           | Stöds           | Stöds           | **Stöds inte**    |
| Java                             | Stöds (V2.0.0+) | Stöds (V2.0.0+) | **Stöds inte**   | **Stöds inte**   | **Stöds inte**    |
| Node.js                          | Stöds (V1.3.0+) | Stöds (V1.3.0+) | **Stöds inte**   | Stöds (V1.3.0+) | **Stöds inte**    |

Grundläggande mått inkluderar begäran, beroende och undantagsfrekvens. Prestandamått (prestandaräknare) inkluderar minne och CPU. Exempeltelemetri visar en ström med detaljerad information för misslyckade begäranden och beroenden, undantag, händelser och spårningar.

 \*Stödet perfCounters varierar något mellan versioner av .NET Core som inte är inriktade på .NET Framework:

- PerfCounters-mått stöds när du kör i Azure App Service för Windows. (AspNetCore SDK Version 2.4.1 eller senare)
- PerfCounters stöds när appen körs i alla Windows-datorer (VM eller molntjänst eller On-prem etc.) (AspNetCore SDK Version 2.7.1 eller senare), men för appar som är inriktade på .NET Core 2.0 eller senare.
- PerfCounters stöds när appen körs var som helst (Linux, Windows, apptjänst för Linux, behållare osv.) i den senaste betaversionen (dvs. AspNetCore SDK Version 2.8.0-beta1 eller senare), men för appar som är inriktade på .NET Core 2.0 eller senare.

Som standard är livemått inaktiverade i Node.js SDK. Om du vill aktivera `setSendLiveMetrics(true)` Live Metrics lägger du till [dina konfigurationsmetoder](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) när du initierar SDK.

## <a name="troubleshooting"></a>Felsökning

Ser du inga data? Om ditt program finns i ett skyddat nätverk: Live Metrics Stream använder andra IP-adresser än andra Program Insights telemetri. Kontrollera att [IP-adresserna](../../azure-monitor/app/ip-addresses.md) är öppna i brandväggen.

## <a name="next-steps"></a>Nästa steg
* [Övervaka användning med Application Insights](../../azure-monitor/app/usage-overview.md)
* [Använda diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Felsökning av ögonblicksbild](../../azure-monitor/app/snapshot-debugger.md)
