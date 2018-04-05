---
title: Live mått ström med anpassade mått och diagnostik i Azure Application Insights | Microsoft Docs
description: Övervaka ditt webbprogram i realtid med anpassade mått och diagnostisera problem med en levande feed fel, spårningar och händelser.
services: application-insights
documentationcenter: ''
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mbullwin
ms.openlocfilehash: f0338642ab99af2fd5ec4f6432bbb8d626daea29
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Direktsänd dataström med mått: Övervaka och diagnostisera med 1 sekund svarstid 

Avsökning pulsslagsstatus centralt i ditt live, i produktion webbprogram med hjälp av mätvärden för Liveströmning från [Programinsikter](app-insights-overview.md). Välj och filtrera mått och prestanda räknare kan du titta på i realtid utan störningar till din tjänst. Kontrollera stackspår från exemplet misslyckades begäranden och undantag. Tillsammans med [Profiler](app-insights-profiler.md), [ögonblicksbild felsökare](app-insights-snapshot-debugger.md), och [prestandatester](app-insights-monitor-web-app-availability.md#performance-tests), direktsänd dataström med mått ger ett kraftfullt och icke-inkräktande diagnostikverktyg för webbplatsen live.

Med direktsänd dataström med mått kan du:

* Verifiera en korrigering medan släpps, genom att titta på prestanda och fel antal.
* Titta på effekten av testa belastningar och diagnostisera problem live. 
* Fokusera på specifika test sessioner eller filtrera bort kända problem genom att välja och filtrera mått som du vill titta på.
* Hämta undantag spårningar när de görs.
* Experimentera med filter för att hitta de mest relevanta KPI: er.
* Övervaka alla Windows prestanda räknaren live.
* Lätt att identifiera en server som har problem och filtrera alla de KPI/live feed till bara den servern.

[![Live mått direktuppspelning av video](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Direktsänd dataström med mått är tillgänglig på ASP.NET-appar som körs lokalt eller i molnet. 

## <a name="get-started"></a>Kom i gång

1. Om du inte gjort ännu [installerat Application Insights](app-insights-asp-net.md) i ditt webbprogram för ASP.NET eller [Windows server-app](app-insights-windows-services.md), göra det nu. 
2. **Uppdatera till den senaste versionen** av Application Insights-paketet. Högerklicka på projektet i Visual Studio och välj **hantera Nuget-paket**. Öppna den **uppdateringar** markerar **inkludera förhandsversion**, och välj de Microsoft.ApplicationInsights.* paket.

    Distribuera om din app.

3. I den [Azure-portalen](https://portal.azure.com), öppna Application Insights-resurs för din app och öppna sedan direktsänd dataström.

4. [Skydda kontrollkanalen](#secure-the-control-channel) om du kan använda känsliga data, till exempel Kundnamn i filter.


![Klicka på direktsänd dataström i bladet översikt](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>Ser du inga data? Kontrollera server-brandvägg

Kontrollera den [utgående portar för Liveströmning mått](app-insights-ip-addresses.md#outgoing-ports) är öppna i brandväggen för dina servrar. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Hur skiljer sig direktsänd dataström med mått från Metrics Explorer och Analytics?

| |Direktsänd ström | Metrics Explorer och analys |
|---|---|---|
|Svarstid|Data som visas i en sekund|Visar det sammanlagda resultatet minuter|
|Inget bevarande|Data kvarstår medan den är på diagrammet och sedan tas bort|[Data som lagras i 90 dagar](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|På begäran|Data strömmas När du öppnar Live mått|Informationen skickas när SDK är installerat och aktiverat|
|Gratis|Det kostar inget direktsänd dataström med data|Föremål för [priser](app-insights-pricing.md)
|Samling|Alla valda mått och räknare överförs. Fel och stackspår samplas. TelemetryProcessors tillämpas inte.|Händelser kan vara [provtagning](app-insights-api-filtering-sampling.md)|
|Kontrollkanal|Filterkontroll signaler skickas till SDK. Vi rekommenderar att du [skydda den här kanalen](#secure-channel).|Kommunikation är enkelriktad till portalen|


## <a name="select-and-filter-your-metrics"></a>Välj och filtrera dina

(Finns på den klassiska ASP.NET appar med den senaste SDK: N.)

Du kan övervaka egna live KPI genom att använda valfri filter på alla Application Insights telemetri från portalen. Klicka på filterkontrollen som visas när du muspekaren över något av scheman. Följande diagram är rita upp en anpassad antalet begäranden KPI med filter på URL: en och varaktighet attribut. Verifiera filter med avsnittet dataströmmen förhandsgranskning som visar en levande feed av telemetri som matchar de villkor som du har angett när som helst i tid. 

![Anpassade begäran KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Du kan övervaka ett annat värde än antalet. Alternativen beror på vilken typ av dataström som kan vara någon Application Insights telemetri: begäranden, beroenden, undantag, spårningar, händelser och mått. Det kan vara dina egna [anpassade mått](app-insights-api-custom-events-metrics.md#properties):

![Alternativ](./media/app-insights-live-stream/live-stream-valueoptions.png)

Du kan också övervaka Windows-prestandaräknare genom att välja som alternativ för dataströmmen och namn på prestandaräknaren förutom Application Insights telemetry.

Live mått aggregeras vid två punkter: lokalt på varje server och sedan på alla servrar. Du kan ändra standard på antingen genom att välja andra alternativ i respektive listrutorna.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Exempel telemetri: Egna Live diagnostiska händelser
Som standard visas händelser live feeden prover av misslyckade begäranden och beroendeanrop, undantag, händelser och spår. Klicka på filterikonen om du vill se kriterierna som används när som helst i tid. 

![Standard live-feed](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Som med statistik och, kan du ange godtycklig kriterier för Application Insights telemetri typer. I det här exemplet väljer vi misslyckade begäranden för specifika, spår och händelser. Vi är att markera alla undantag och beroende fel.

![Egna live feed](./media/app-insights-live-stream/live-stream-events.png)

Obs: För närvarande för undantaget meddelandebaserad villkor, använder det yttersta Undantagsmeddelandet. I föregående exempel, att filtrera ut ofarlig undantag med meddelandet i det interna undantaget (följer den ”<--” avgränsare) ”klienten kopplas från”. Använd ett meddelande innehåller inte-”fel vid läsning av innehållet i en begäran” villkor.

Visa information om ett objekt i denna feed genom live genom att klicka på den. Du kan pausa feeden genom att klicka på **pausa** bara bläddring nedåt eller klicka på ett element. Live feed återupptas när du bläddrar överst på sidan eller genom att klicka på räknaren av objekt som samlas in när den har pausats.

![Provade live fel](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrera efter server-instans

Om du vill övervaka en viss instans av serverroll kan du filtrera av servern.

![Provade live fel](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK-krav
Egna Live mått är tillgängliga med version 2.4.0-beta2 eller senare av [Application Insights SDK för webbtjänst](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Kom ihåg att välja alternativet ”Inkludera förhandsversion” från NuGet-Pakethanteraren.

## <a name="secure-the-control-channel"></a>Skydda kontrollkanalen
De Anpassa filter kriterier som du anger skickas tillbaka till komponenten Live mått i Application Insights SDK. Filter kan innehålla känslig information, till exempel customerIDs. Du kan göra kanalen säker med en hemlig nyckel API förutom instrumentation nyckeln.
### <a name="create-an-api-key"></a>Skapa en API-nyckel

![Skapa api-nyckel](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Lägg till API-nyckeln i konfigurationen

# <a name="net-standardtabnet-standard"></a>[.NET-standard](#tab/.net-standard)

Lägg till AuthenticationApiKey QuickPulseTelemetryModule i applicationinsights.config-filen:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Eller i koden, Ställ in den på QuickPulseTelemetryModule:

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```
# <a name="net-core-tabnet-core"></a>[.NET Core] (#tab/.net-core)

Ändra filen startup.cs på följande sätt:

Lägg först till

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;
```

Sedan konfigurera metoden Lägg till under:

``` C#
  QuickPulseTelemetryModule dep;
            var modules = app.ApplicationServices.GetServices<ITelemetryModule>();
            foreach (var module in modules)
            {
                if (module is QuickPulseTelemetryModule)
                {
                    dep = module as QuickPulseTelemetryModule;
                    dep.AuthenticationApiKey = "YOUR-API-KEY-HERE";
                    dep.Initialize(TelemetryConfiguration.Active);
                }
            }
```

---

Om du känner igen och litar till anslutna servrar kan du försöka anpassade filter utan autentiserade kanalen. Det här alternativet är tillgängligt i sex månader. Den här åsidosättningen krävs när varje ny session, eller när en ny server är online.

![Live mått Auth-alternativ](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>Vi rekommenderar starkt att du ställer in autentiserade kanalen innan du anger vara känslig information, till exempel CustomerID i filtervillkoren.
>

## <a name="generating-a-performance-test-load"></a>Generera en prestandabelastningen test

Om du vill titta på effekten av en ökad belastning använda bladet prestandatest. Den simulerar begäranden från ett antal samtidiga användare. Det kan köras antingen ”manuell test” (ping tester) på en enskild URL eller det kan köras en [flera steg prestanda webbtest](app-insights-monitor-web-app-availability.md#multi-step-web-tests) som du överför (på samma sätt som ett tillgänglighetstest).

> [!TIP]
> När du har skapat prestandatesten öppna testet och bladet direktsänd dataström i separata fönster. Du kan se när köade prestandatester startar och titta på direktsänd dataström i taget.
>


## <a name="troubleshooting"></a>Felsökning

Ser du inga data? Om programmet är i ett skyddat nätverk: Live mått dataströmmen använder en annan IP-adresser än andra Application Insights telemetry. Kontrollera att [IP-adresserna](app-insights-ip-addresses.md) är öppna i brandväggen.



## <a name="next-steps"></a>Nästa steg
* [Övervakning med Application Insights](app-insights-web-track-usage.md)
* [Diagnostiska sökning](app-insights-diagnostic-search.md)
* [Profiler](app-insights-profiler.md)
* [Snapshot-felsökare](app-insights-snapshot-debugger.md)
