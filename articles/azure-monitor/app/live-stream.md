---
title: Live Metrics Stream med anpassade mått och diagnostik i Azure Application Insights | Microsoft Docs
description: Övervaka din webbapp i real tid med anpassade mått och diagnostisera problem med en direkt flöde av fel, spår och händelser.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: d85688d297eb0df00e71f388b2a3350eabe5f6d5
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817199"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: övervaka & diagnostisera med en andra svars tid

Använd Live Metrics Stream från [Application Insights](../../azure-monitor/app/app-insights-overview.md)för att avhjälpa det taktiga hjärtat i din Live-, produktions webb tillämpning. Välj och filtrera mått och prestanda räknare för att se Real tid utan störningar på tjänsten. Undersök stack spårningar från exempel misslyckade förfrågningar och undantag. Tillsammans med [profiler](../../azure-monitor/app/profiler.md), [ögonblicks bilds fel sökning](../../azure-monitor/app/snapshot-debugger.md). Live Metrics Stream ger en kraftfull och icke-invasiv diagnos verktyg för din Live-webbplats.

Med Live Metrics Stream kan du:

* Verifiera en åtgärd när den släpps genom att titta på prestanda-och antal haverier.
* Se resultatet av test belastningar och diagnostisera problem Live. 
* Fokusera på specifika testsessioner eller filtrera bort kända problem genom att välja och filtrera de mått som du vill titta på.
* Hämta undantags spårningar när de sker.
* Experimentera med filter för att hitta de mest relevanta KPI: erna.
* Övervaka alla Windows prestanda räknare Live.
* Identifiera enkelt en server som har problem och filtrera alla KPI/Live-flöden på bara den servern.

[![Live Metrics Stream video](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Live-mått stöds för närvarande för ASP.NET-, ASP.NET Core-, Azure Functions-, Java-och Node. js-appar.

## <a name="get-started"></a>Kom i gång

1. Om du ännu inte har [installerat Application Insights](../../azure-monitor/azure-monitor-app-hub.md) i din webbapp gör du det nu.
2. Förutom standard Application Insightss paketen [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) krävs för att aktivera Live Metrics Stream.
3. **Uppdatera till den senaste versionen** av Application Insights-paketet. I Visual Studio högerklickar du på projektet och väljer **Hantera NuGet-paket**. Öppna fliken **uppdateringar** och markera alla Microsoft. ApplicationInsights. *-paket.

    Distribuera om din app.

3. I [Azure Portal](https://portal.azure.com)öppnar du Application Insights resurs för din app och öppnar sedan Live Stream.

4. [Skydda kontroll kanalen](#secure-the-control-channel) om du kan använda känsliga data som kund namn i dina filter.

### <a name="nodejs"></a>Node.js

Om du vill använda Live-mått med Node. js måste du uppdatera till version 1,30 eller senare av SDK: n. Som standard är Live-måtten inaktiverade i Node. js SDK. Om du vill aktivera Live-mått lägger du till `setSendLiveMetrics(true)` i dina [konfigurations metoder](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) när du initierar SDK: n.

### <a name="no-data-check-your-server-firewall"></a>Inga data? Kontrol lera serverns brand vägg

Kontrol lera att de [utgående portarna för Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) är öppna i brand väggen för dina servrar. 

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Hur skiljer sig Live Metrics Stream från Metrics Explorer och analyser?

| |Direktsänd ström | Metrics Explorer och analys |
|---|---|---|
|Svarstid|Data som visas inom en sekund|Sammanställt över minuter|
|Ingen kvarhållning|Data sparas medan de finns i diagrammet och ignoreras sedan|[Data kvarhållna i 90 dagar](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|På begäran|Data strömmas när du öppnar Live-mått|Data skickas när SDK är installerat och aktiverat|
|Kostnadsfri|Det kostar inget att Live Stream data|Omfattas av [prissättning](../../azure-monitor/app/pricing.md)
|Sampling|Alla valda mått och räknare överförs. Felen och stack spåren samplas. TelemetryProcessors tillämpas inte.|Händelser kan [samplas](../../azure-monitor/app/api-filtering-sampling.md)|
|Kontroll kanal|Filter kontroll signaler skickas till SDK: n. Vi rekommenderar att du skyddar den här kanalen.|Kommunikationen är enkelriktad, till portalen|

## <a name="select-and-filter-your-metrics"></a>Välj och filtrera dina mått

(Tillgänglig med ASP.NET, ASP.NET Core och Azure Functions (v2).)

Du kan övervaka anpassad KPI Live genom att använda godtyckliga filter på alla Application Insights telemetri från portalen. Klicka på den filter kontroll som visas när du pekar på ett diagram. Följande diagram ritar en anpassad KPI för antalet begär Anden med filter på URL-och duration-attribut. Verifiera dina filter med avsnittet för hands version av data ström som visar en live-feed med telemetri som matchar de kriterier som du har angett vid en viss tidpunkt. 

![KPI för anpassad begäran](./media/live-stream/live-stream-filteredMetric.png)

Du kan övervaka ett värde som skiljer sig från antalet. Vilka alternativ som är tillgängliga beror på vilken typ av data ström som kan vara en Application Insights telemetri: begär Anden, beroenden, spårningar, händelser eller mått. Det kan vara din egen [anpassade mått enhet](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Värde alternativ](./media/live-stream/live-stream-valueoptions.png)

Förutom att Application Insights telemetri kan du också övervaka Windows prestanda räknare genom att välja det från data Ströms alternativen och ange namnet på prestanda räknaren.

Live-mått sammanställs på två punkter: lokalt på varje server och sedan på alla servrar. Du kan ändra standardvärdet genom att välja andra alternativ i respektive listruta.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Exempel på telemetri: anpassade Live Diagnostic-händelser
Som standard visar live-matningen av händelser exempel på misslyckade förfrågningar och beroende anrop, undantag, händelser och spår. Klicka på filter ikonen för att se de tillämpade kriterierna vid varje tidpunkt. 

![Standard-live-feed](./media/live-stream/live-stream-eventsdefault.png)

Precis som med mått kan du ange valfria villkor till vilken typ av Application Insights telemetri som helst. I det här exemplet väljer vi vissa förfrågningar, spårningar och händelser. Vi väljer också alla undantag och beroende fel.

![Anpassad live-feed](./media/live-stream/live-stream-events.png)

Obs: för närvarande, för undantags meddelande villkor, använder du det yttersta undantags meddelandet. I föregående exempel kan du filtrera bort det ofarliga undantaget med ett internt undantags meddelande (efter "<--"-avgränsare) "klienten kopplades från." Använd meddelandet not-contains "fel vid läsning av begär ande innehåll".

Se information om ett objekt i Live-flödet genom att klicka på det. Du kan pausa matningen antingen genom att klicka på **pausa** eller helt enkelt rulla nedåt eller klicka på ett objekt. Live-matningen återupptas när du har rullat överst eller genom att klicka på objekt räknaren som samlats in när den pausades.

![Exempel på aktiva startfel](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrera efter Server instans

Om du vill övervaka en viss server roll instans kan du filtrera efter server.

![Exempel på aktiva startfel](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Krav för SDK

### <a name="net"></a>.NET
Anpassad Live Metrics Stream tillgänglig med version 2.4.0-beta2 eller senare av [Application Insights SDK för webben](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Kom ihåg att välja alternativet "ta med för hands version" från NuGet Package Manager.

### <a name="nodejs"></a>Node.js
Live Metrics Stream är tillgängligt med version 1.3.0 eller senare av [Application Insights SDK för Node. js](https://npmjs.com/package/applicationinsights). Kom ihåg att använda `setSendLiveMetrics(true)` när du konfigurerar SDK i din kod.

## <a name="secure-the-control-channel"></a>Skydda kontroll kanalen
De anpassade filter kriterier som du anger skickas tillbaka till komponenten Live Metrics i Application Insights SDK. Filtren kan eventuellt innehålla känslig information, till exempel customerID. Du kan göra kanalen säker med en hemlig API-nyckel förutom Instrumentation-nyckeln.
### <a name="create-an-api-key"></a>Skapa en API-nyckel

![Skapa API-nyckel](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Lägg till API-nyckel i konfigurationen

### <a name="classic-aspnet"></a>Klassisk ASP.NET

I filen applicationinsights. config lägger du till AuthenticationApiKey i QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Eller i kod, ställer du in den på QuickPulseTelemetryModule:

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

### <a name="azure-function-apps"></a>Azure Function-appar

För Azure Function Apps (v2) är det möjligt att skydda kanalen med en API-nyckel med en miljö variabel. 

Skapa en API-nyckel inifrån din Application Insights-resurs och gå till **program inställningar** för din Funktionsapp. Välj **Lägg till ny inställning** och ange ett namn på `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` och ett värde som motsvarar din API-nyckel.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (kräver Application Insights ASP.NET Core SDK 2.3.0 – beta eller senare)

Ändra din startup.cs-fil på följande sätt:

Börja med att lägga till

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Lägg sedan till följande i metoden ConfigureServices:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Men om du känner igen och litar på alla anslutna servrar kan du prova anpassade filter utan den autentiserade kanalen. Det här alternativet är tillgängligt i sex månader. Den här åsidosättningen krävs en gång i varje ny session, eller när en ny server är online.

![Alternativ för Live Metrics-autentisering](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Vi rekommenderar starkt att du konfigurerar den autentiserade kanalen innan du anger potentiellt känslig information som CustomerID i filter kriterierna.
>

## <a name="troubleshooting"></a>Felsöka

Inga data? Om ditt program finns i ett skyddat nätverk: Live Metrics Stream använder andra IP-adresser än andra Application Insights telemetri. Kontrol lera att [de här IP-adresserna](../../azure-monitor/app/ip-addresses.md) är öppna i brand väggen.



## <a name="next-steps"></a>Nästa steg
* [Övervaka användning med Application Insights](../../azure-monitor/app/usage-overview.md)
* [Använda diagnostisk sökning](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Fel sökning av ögonblicks bild](../../azure-monitor/app/snapshot-debugger.md)
