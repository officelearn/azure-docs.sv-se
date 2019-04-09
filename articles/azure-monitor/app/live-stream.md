---
title: Live Metrics Stream med anpassade mått och diagnostik i Azure Application Insights | Microsoft Docs
description: Övervaka din webbapp i realtid med anpassade mått och diagnostisera problem med en live feed med fel, spårningar och händelser.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/28/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 588b8b11a02551a790145aafb013759699004267
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009973"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Övervaka och diagnostisera med en svarstid på 1 sekund

Avsökning datorer centralt i webbprogrammet live, i produktion med hjälp av Live Metrics Stream från [Application Insights](../../azure-monitor/app/app-insights-overview.md). Välja och filtrera statistik och prestandaräknare kan du titta på i realtid, utan störningar i tjänsten. Granska stackspårningar från exemplet misslyckades begäranden och undantag. Tillsammans med [Profiler](../../azure-monitor/app/profiler.md), [Snapshot debugger](../../azure-monitor/app/snapshot-debugger.md), och [prestandatestning](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests), Live Metrics Stream ger en kraftfull och icke-inkräktande diagnostikverktyg för webbplatsen plats.

Med Live Metrics Stream kan du:

* Verifiera en korrigering medan den släpps genom att titta på antal prestanda och fel.
* Se effekten av testa laster och diagnostisera problem live. 
* Fokusera på specifika test sessioner eller filtrera bort kända problem genom att välja och filtrera de mått som du vill se.
* Få undantag spårningar när de visar.
* Experimentera med filter för att hitta de mest relevanta KPI: er.
* Övervaka alla Windows prestanda räknaren live.
* Enkelt identifiera en server som har problem och filtrera alla de KPI/live-flöde till bara den servern.

[![Livt Metrics Stream-video](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Livemått stöds för närvarande för ASP.NET, ASP.NET Core, Azure Functions, Java och Node.js-appar.

## <a name="get-started"></a>Kom igång

1. Om du inte gjort ännu [installera Application Insights](../../azure-monitor/azure-monitor-app-hub.md) i din webbapp, gör du det nu.
2. Förutom standardpaket för Application Insights [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) krävs för att aktivera Live Metrics stream.
3. **Uppdatera till den senaste versionen** av Application Insights-paketet. I Visual Studio högerklickar du på projektet och välj **hantera Nuget-paket**. Öppna den **uppdateringar** och sedan alla Microsoft.ApplicationInsights.*-paket.

    Distribuera om din app.

3. I den [Azure-portalen](https://portal.azure.com), öppna Application Insights-resurs för din app och sedan öppna Live Stream.

4. [Skydda kontrollkanalen](#secure-the-control-channel) om du kan använda känsliga data, till exempel Kundnamn i dina filter.

### <a name="no-data-check-your-server-firewall"></a>Ser du inga data? Kontrollera serverbrandväggen

Kontrollera den [utgående portar för Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) är öppen i brandväggen för dina servrar. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Hur skiljer sig Live Metrics Stream från Metrics Explorer och Analytics?

| |Direktsänd ström | Metrics Explorer och analys |
|---|---|---|
|Svarstid|Data som visas inom en sekund|Sammanlagda minuter|
|Inga kvarhållning|Data kvarstår medan den i diagrammet och sedan tas bort|[Data bibehålls i 90 dagar](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|På begäran|Data som strömmas medan du öppna Live Metrics|Informationen skickas när SDK är installerat och aktiverat|
|Kostnadsfri|Det kostar inget Live Stream-data|Lyder [priser](../../azure-monitor/app/pricing.md)
|Samling|Alla valda mått och räknare överförs. Fel och stackspår samplas. TelemetryProcessors tillämpas inte.|Händelser kan vara [samplas](../../azure-monitor/app/api-filtering-sampling.md)|
|Kontrollkanal|Filterkontroll signaler skickas till SDK: N. Vi rekommenderar att du skyddar den här kanalen.|Kommunikationen är enkelriktade på portalen|


## <a name="select-and-filter-your-metrics"></a>Välja och filtrera dina mått

(Tillgängligt med ASP.NET, ASP.NET Core och Azure Functions (v2).)

Du kan övervaka anpassade KPI live genom att använda godtyckliga filter på någon Application Insights-telemetri från portalen. Klicka på filterkontroll som visar när du över något av scheman. Följande diagram är ritning en anpassad antal förfrågningar KPI med filter på URL: en och varaktighet attribut. Verifiera dina filter med avsnittet Stream förhandsversion som visar en direktsändningen av telemetri som matchar de villkor som du har angett när som helst i tid. 

![Custom Request KPI](./media/live-stream/live-stream-filteredMetric.png)

Du kan övervaka ett värde som skiljer sig från antalet. Vilka alternativ som beror på vilken typ av strömmar, vilket kan vara någon telemetri med Application Insights: förfrågningar, beroenden, undantag, spår, händelser eller mått. Det kan vara ditt eget [anpassade mått](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Värdealternativ för](./media/live-stream/live-stream-valueoptions.png)

Du kan också övervaka alla Windows-prestandaräknare genom att välja som stream-alternativ och att ange namnet på prestandaräknaren förutom Application Insights telemetry.

Livemått räknas samman på två punkter: lokalt på varje server och sedan på alla servrar. Du kan ändra standardinställningen på antingen genom att välja andra alternativ i respektive listrutorna.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Exempel-telemetri: Anpassad Live diagnostikhändelser
Som standard visar direktsändningen händelser prover av misslyckade begäranden och beroendeanrop, undantag, händelser och spår. Klicka på filterikonen för att se de tillämpade kriterierna när som helst i tid. 

![Standard direktsändningen](./media/live-stream/live-stream-eventsdefault.png)

Som med mätvärden, kan du ange godtyckliga kriterier för någon av typerna av telemetri för Application Insights. I det här exemplet väljer vi misslyckade specifika begäranden, spårningar och händelser. Vi är att markera alla undantag och beroendefel.

![Anpassade direktsändningen](./media/live-stream/live-stream-events.png)

Obs! För närvarande kan använda det yttersta Undantagsmeddelandet för meddelandebaserad kriterier för undantag. I föregående exempel, att filtrera bort ofarliga undantag med meddelandet om internt Undantagsmeddelande (följer den ”<--” avgränsare) ”klient byl odpojen”. Använd ett meddelande innehåller inte-”fel vid läsning av innehållet i begäran” villkor.

Se information om ett objekt i live flödet genom att klicka på den. Du kan pausa flödet genom att klicka på **pausa** eller helt enkelt bläddra nedåt, eller klickar på ett objekt. Direktsändningen kommer att återupptas när du bläddrar överst på sidan eller genom att klicka på räknaren av objekt som samlas in när den har pausats.

![Samplat live fel](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrera efter server-instans

Om du vill övervaka en viss server-rollinstans kan du filtrera efter server.

![Samplat live fel](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Krav för SDK
Anpassade Live Metrics Stream är tillgängligt med version 2.4.0-beta2 eller senare av [Application Insights SDK för webbtjänst](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Kom ihåg att välja alternativet ”Inkludera förhandsversion” från NuGet-Pakethanteraren.

## <a name="secure-the-control-channel"></a>Skydda kontrollkanal
De Anpassa filter kriterier som du anger skickas tillbaka till komponenten Live Metrics i Application Insights SDK. Filtren kan innehålla känslig information, till exempel customerIDs. Du kan göra kanalen säker med en hemlig nyckel API förutom instrumenteringsnyckeln.
### <a name="create-an-api-key"></a>Skapa en API-nyckel

![Skapa API-nyckel](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Lägg till API-nyckeln i konfigurationen

### <a name="classic-aspnet"></a>Klassiska ASP.NET

Lägg till AuthenticationApiKey QuickPulseTelemetryModule i filen applicationinsights.config:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Eller i koden, anger du den på QuickPulseTelemetryModule:

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

### <a name="azure-function-apps"></a>Azure Function Apps

Nyckeln kan åstadkommas med en miljövariabel för Azure Functions-appar (v2) skydda kanalen med ett API. 

Skapa en API-nyckel från Application Insights-resursen och gå till **programinställningar** för din Funktionsapp. Välj **Lägg till ny inställning** och ange ett namn för `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` och ett värde som motsvarar din API-nyckel.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (kräver Application Insights ASP.NET Core SDK 2.3.0-beta eller senare)

Ändra din startup.cs-fil på följande sätt:

Lägg först till

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Sedan i metoden ConfigureServices lägger du till:

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Om du känner igen och litar på alla anslutna servrar, kan du försöka anpassade filter utan autentiserade kanalen. Det här alternativet är tillgängligt i sex månader. Den här åsidosättningen måste anges en gång varje ny session, eller när en ny server är online.

![Live Metrics Auth-alternativ](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Vi rekommenderar starkt att du ställer in autentiserade kanalen innan potentiellt känslig information som CustomerID i filtervillkoren.
>

## <a name="generating-a-performance-test-load"></a>Generera en belastning för test av prestanda

Om du vill se effekten av en ökning av belastningen använda bladet prestandatest. Den simulerar begäranden från ett antal samtidiga användare. Det kan köras antingen ”manuella tester” (ping tester) av en enskild URL eller den kan köra en [flerstegstest prestandatest](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) som du överför (på samma sätt som ett tillgänglighetstest).

> [!TIP]
> När du har skapat prestandatest öppna testet och bladet Live Stream i olika fönster. Du kan se när köade prestandatest startar och titta på live stream samtidigt.
>


## <a name="troubleshooting"></a>Felsökning

Ser du inga data? Om programmet är i ett skyddat nätverk: Live Metrics Stream använder en annan IP-adresser än andra Application Insights telemetry. Se till att [de IP-adresserna](../../azure-monitor/app/ip-addresses.md) är öppen i brandväggen.



## <a name="next-steps"></a>Nästa steg
* [Övervakning med Application Insights](../../azure-monitor/app/usage-overview.md)
* [Med hjälp av Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)
* [Profilerare](../../azure-monitor/app/profiler.md)
* [Felsökningsprogram för ögonblicksbilder](../../azure-monitor/app/snapshot-debugger.md)
