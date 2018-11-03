---
title: Azure Application Insights vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor om Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: mbullwin
ms.openlocfilehash: 60195f135b8b0e102a36c3573a341432f2e0c784
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959315"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Vanliga frågor och svar

## <a name="configuration-problems"></a>Konfigurationsproblem
*Jag har problem med inställningen min:*

* [.NET-app](app-insights-asp-net-troubleshoot-no-data.md)
* [Övervaka en app redan körs](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Azure-diagnostik](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)
* [Java-webbapp](app-insights-java-troubleshoot.md)

*Jag får inga data från Min server*

* [Brandväggsundantag för set](app-insights-ip-addresses.md)
* [Konfigurera en ASP.NET-server](app-insights-monitor-performance-live-website-now.md)
* [Konfigurera en server med Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Kan jag använda Application Insights med...?

* [Webbappar på en IIS-server – lokalt eller i en virtuell dator](app-insights-asp-net.md)
* [Java-webbappar](app-insights-java-get-started.md)
* [Node.js-appar](app-insights-nodejs.md)
* [Web apps på Azure](app-insights-azure-web-apps.md)
* [Molntjänster i Azure](app-insights-cloudservices.md)
* [App-servrar som körs i Docker](app-insights-docker.md)
* [Enkelsidigt webbappar](app-insights-javascript.md)
* [SharePoint](app-insights-sharepoint.md)
* [Windows-skrivbordsapp](app-insights-windows-desktop.md)
* [Andra plattformar](app-insights-platforms.md)

## <a name="is-it-free"></a>Är det gratis?

Ja, för experiment. I grundläggande prisplanen, kan ditt program skickar en vissa datakvoten varje månad kostnadsfritt. Den kostnadsfria tilldelningen är tillräckligt stor för att cover utvecklings- och publicera en app för ett litet antal användare. Du kan ange ett tak för att förhindra att fler än en angiven mängd data som bearbetas.

Större mängder telemetri debiteras Gb. Vi tillhandahåller några tips på hur du [begränsa dina kostnader för](app-insights-pricing.md).

Enterprise-avtalet utgår en avgift för varje dag som varje webbservernoden skickar telemetri. Det är lämpligt om du vill använda löpande Export i stor skala.

[Läsa prisplanen](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Hur mycket det kostar?

* Öppna den **användning och uppskattade kostnader** sida i en Application Insights-resurs. Det finns ett diagram över de senaste användning. Du kan ange ett tak för datavolymen, om du vill.
* Öppna den [Azure-fakturering bladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) att se dina fakturor över alla resurser.

## <a name="q14"></a>Vad ändras Application Insights i projektet?
Informationen beror på vilken typ av projekt. För ett webbprogram:

* Lägger till de här filerna i projektet:

  * ApplicationInsights.config.
  * ai.js
* Installerar dessa NuGet-paket:

  * *Application Insights API* -core API
  * *Application Insights API för webbprogram* – används för att skicka telemetri från servern
  * *Application Insights API för JavaScript-program* – används för att skicka telemetri från klienten

    Paket som innehåller dessa sammansättningar:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Infogar objekt i:

  * Web.config
  * Packages.config
* (Nytt projekt – endast om du [Lägg till Application Insights till ett befintligt projekt][start], du behöver göra detta manuellt.) Infogar kodfragment i koden för klienten och servern att initiera dem med Application Insights-resurs-ID. Till exempel i en MVC-app matas kod in huvudsida Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hur uppgraderar jag från äldre versioner av SDK?
Se den [viktig](app-insights-release-notes.md) för SDK som är lämpliga för ditt program.

## <a name="update"></a>Hur ändrar jag mitt projekt skickar data till vilken Azure-resurs?
I Solution Explorer högerklickar du på `ApplicationInsights.config` och välj **Update Application Insights**. Du kan skicka data till en befintlig eller ny resurs i Azure. Uppdateringsguiden ändrar instrumenteringsnyckeln i ApplicationInsights.config, vilket avgör där servern SDK skickar dina data. Om du avmarkerar ”uppdatera alla” ändras också nyckeln där visas i dina webbsidor.

## <a name="what-is-status-monitor"></a>Vad är Statusövervakaren?

En skrivbordsapp som du kan använda i IIS-webbservern för att konfigurera Application Insights i web apps. Den samlar inte in telemetri: du kan stoppa den när du inte konfigurerar en app. 

[Läs mer](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Vilken telemetri samlas in av Application Insights?

Från server web apps:

* HTTP-begäranden
* [Beroenden](app-insights-asp-net-dependencies.md). Anrop till: SQL-databaser. HTTP-anrop till externa tjänster. Azure Cosmos DB, tabell, blob-lagring och kö. 
* [Undantag](app-insights-asp-net-exceptions.md) och Stacka spårningar.
* [Prestandaräknare](app-insights-performance-counters.md) – om du använder [statusövervakaren](app-insights-monitor-performance-live-website-now.md), [azureövervakning](app-insights-azure-web-apps.md) eller [Application Insights insamlade skrivaren](app-insights-java-collectd.md).
* [Anpassade händelser och mått](app-insights-api-custom-events-metrics.md) du koda.
* [Spårningsloggar](app-insights-asp-net-trace-logs.md) om du konfigurerar lämpliga insamlaren.

Från [klienten webbsidor](app-insights-javascript.md):

* [Antal sidvisningar](app-insights-usage-overview.md)
* [AJAX-anrop](app-insights-asp-net-dependencies.md) begäranden som görs från ett skript som körs.
* Läs in data om sidvisningar
* Antal användare och sessioner
* [Autentiserat användar-ID](app-insights-api-custom-events-metrics.md#authenticated-users)

Från andra källor, om du konfigurerar dem:

* [Azure-diagnostik](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)
* [Docker-behållare](app-insights-docker.md)
* [Importera tabellerna till Analytics](app-insights-analytics-import.md)
* [Log Analytics](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan jag filtrera bort eller ändra telemetri?

Ja, i den server som du kan skriva:

* Telemetri Processor att filtrera eller lägga till egenskaper i valda telemetri objekt innan de skickas från din app.
* Telemetriinitieraren att lägga till egenskaper i alla objekt i telemetrin.

Lär dig mer om [ASP.NET](app-insights-api-filtering-sampling.md) eller [Java](app-insights-java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Hur beräknas ort, land och andra data för geo-plats?

Vi ser IP-adress (IPv4 eller IPv6) för webbklienten med [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Webbläsartelemetri: vi samlar in avsändarens IP-adress.
* Telemetri: The Application Insights-modulen samlar in klientens IP-adress. Den har inte samlats in om `X-Forwarded-For` har angetts.

Du kan konfigurera den `ClientIpHeaderTelemetryInitializer` att ta den IP-adressen från en annan rubrik. I vissa system, till exempel den flyttas av en proxy, läsa in belastningsutjämnare eller CDN för att `X-Originating-IP`. [Läs mer](http://apmtips.com/blog/2016/07/05/client-ip-address/).

Du kan [använda Power BI](app-insights-export-power-bi.md) att visa din begärandetelemetri på en karta.


## <a name="data"></a>Hur länge sparas data i portalen? Är den säker?
Ta en titt på [kvarhållning av Data och sekretess][data].

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>Kan personligt identifierbar information (PII) skickas i telemetri?

Detta är möjligt om koden skickar dessa data. Det kan också inträffa om variabler i stackspårningar innehåller personligt identifierbar information. Utvecklingsteamet bör göra riskbedömningar för att säkerställa att personligt identifierbar information hanteras korrekt. [Mer information om kvarhållning av data och sekretess](app-insights-data-retention-privacy.md).

**Alla** oktetterna i webbadressen klienten alltid är inställda på 0 när attribut för geoplats slås upp.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>Min iKey syns i min webbsida-källan. 

* Detta är vanligt vid övervakning av lösningar.
* Det kan inte användas för att stjäla dina data.
* Det kan användas för att förskjuta dina data eller utlösa aviseringar.
* Vi har inte hört att en kund har haft sådana problem.

Du kan:

* Använd två separata iKeys (separata Application Insights-resurser), för klient- och data. Eller
* Skriv en proxy som körs i din server och har webbklienten skicka data via den proxyn.

## <a name="post"></a>Hur ser jag postdata i diagnostiksökning?
Vi inte logga postdata automatiskt, men du kan använda ett TrackTrace anrop: placera data i parametern meddelande. Detta har en längre storleksgräns än begränsningar i egenskaperna för anslutningssträngen, men du inte kan filtrera på den.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Ska jag använda en eller flera Application Insights-resurser?

Använda en enskild resurs för alla komponenter eller roller i en enda affärssystem. Använd separata resurser för utveckling, testning och versioner och för oberoende program.

* [Finns i avsnittet här](app-insights-separate-resources.md)
* [Exempel – molntjänst med worker och webbtjänst roller](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hur ändrar jag instrumenteringsnyckeln dynamiskt?

* [Beskrivning här](app-insights-separate-resources.md)
* [Exempel – molntjänst med worker och webbtjänst roller](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Vad är användaren och Session räknar?

* JavaScript SDK anger en användare cookie på webbklienten att identifiera användare som återvänder, och en sessions-cookie till gruppaktiviteter.
* Om det finns inga klientskript, kan du [angetts cookies på servern](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Om en verklig användare använder webbplatsen på olika webbläsare eller via i läget privat/inkognito surfning eller olika datorer och de kommer att räknas mer än en gång.
* För att identifiera en inloggad användare på alla datorer och webbläsare, lägger du till ett anrop till [setAuthenticatedUserContext()](app-insights-api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> Har jag aktiverat allt innehåll i Application Insights?
| Vad du bör se | Så här hämtar du den | Varför du vill ha dem |
| --- | --- | --- |
| Tillgänglighet diagram |[Webbtester](app-insights-monitor-web-app-availability.md) |Vet webbappen är igång |
| Server app perf: svarstider,... |[Lägg till Application Insights i projektet](app-insights-asp-net.md) eller [installera AI Status Monitor på servern](app-insights-monitor-performance-live-website-now.md) (eller Skriv egen kod till [spåra beroenden](app-insights-api-custom-events-metrics.md#trackdependency)) |Identifiera perf-problem |
| Beroendetelemetri |[Installera AI Status Monitor på servern](app-insights-monitor-performance-live-website-now.md) |Diagnostisera problem med databaser eller andra externa komponenter |
| Få stackspårningar från undantag |[Infoga TrackException anrop i din kod](app-insights-asp-net-exceptions.md) (men vissa rapporteras automatiskt) |Identifiera och diagnostisera undantag |
| Sök loggspårningar |[Lägg till ett kort för loggning](app-insights-asp-net-trace-logs.md) |Diagnostisera undantag, perf-problem |
| Grunderna för användning av klienten: sidvisningar, sessioner,... |[Initierare JavaScript i webbsidor](app-insights-javascript.md) |Användningsanalys |
| Klienten anpassade mått |[Spåra anrop i webbsidor](app-insights-api-custom-events-metrics.md) |Förbättra användarupplevelsen |
| Anpassade mått för Server |[Spårningsanrop i server](app-insights-api-custom-events-metrics.md) |Business Intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Varför är antalen i Sök- och mått som är ojämn?

[Sampling](app-insights-sampling.md) minskar antalet telemetri objekt (begäranden, anpassade händelser och så vidare) som faktiskt skickas från din app till portalen. I Search kan du se antalet objekt som faktiskt togs emot. I mått diagram som visar ett antal händelser, kan du se antalet ursprungliga händelser som inträffade. 

Varje objekt som överförs snabbare sätt att utföra en `itemCount` egenskap som visar hur många ursprungliga händelser objektet representerar. Om du vill se linjer i åtgärden kan du köra den här frågan i Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Konfiguration av Application Insights

Du kan [skriva PowerShell-skript](app-insights-powershell.md) med hjälp av Azure Resource Monitor för att:

* Skapa och uppdatera Application Insights-resurser.
* Ange prisplanen.
* Hämta instrumenteringsnyckeln.
* Lägg till en metrisk varning.
* Lägg till ett tillgänglighetstest.

Du kan inte ställa in en rapport för Metric Explorer eller konfigurera löpande export.

### <a name="querying-the-telemetry"></a>Fråga telemetri

Använd den [REST API](https://dev.applicationinsights.io/) att köra [Analytics](app-insights-analytics.md) frågor.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Hur kan jag för att ställa in dataaviseringar på en händelse?

Azure-aviseringar är bara för mått. Skapa ett anpassat mått som överskrider ett tröskelvärde för värde när din händelsen inträffar. Sedan ställer du in en avisering på måttet. Observera: du får ett meddelande varje gång måtten överskrider tröskelvärdet i båda riktningarna; Du kommer inte få ett meddelande till den första överbrygga, oavsett om det initiala värdet är hög eller låg; Det finns alltid en fördröjning på några minuter.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Finns det några kostnader för dataöverföring mellan en Azure-webbapp och Application Insights?

* Om Azure-webbappen finns i ett datacenter där det finns en slutpunkt för Application Insights-samling, det är kostnadsfritt. 
* Om det finns inga insamlingsslutpunkten i datacentret värden kommer din Apps telemetri medför [Azure utgående avgifter](https://azure.microsoft.com/pricing/details/bandwidth/).

Det här inte är beroende där Application Insights-resursen finns. Det beror på distributionen av våra slutpunkter.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan jag skicka telemetri till Application Insights-portalen?

Vi rekommenderar att du använder våra SDK: er och använder den [SDK API](app-insights-api-custom-events-metrics.md). Det finns olika varianter av SDK för olika [plattformar](app-insights-platforms.md). Dessa SDK: er hantera buffring, komprimering, begränsning, återförsök och så vidare. Men den [inmatning schemat](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) och [endpoint protokollet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) är offentliga.

## <a name="can-i-monitor-an-intranet-web-server"></a>Kan jag övervaka en intranät-webbserver?

Här följer två metoder:

### <a name="firewall-door"></a>Dörren för brandvägg

Tillåter webbservern att skicka telemetri till vår slutpunkter https://dc.services.visualstudio.com:443 och https://rt.services.visualstudio.com:443. 

### <a name="proxy"></a>Proxy

Dirigera trafik från servern till en gateway i intranätet genom overwritting inställningarna i det här exemplet ApplicationInsights.config. Om egenskaperna ”slutpunkten” inte finns i din konfiguration, använder de här klasserna standardvärden som visas i exemplet nedan.

#### <a name="example-applicationinsightsconfig"></a>Exempel ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <TelemetryChannel>
         <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
    </TelemetryChannel>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

_Obs ApplicationIdProvider är tillgänglig från och med v2.6.0_

Din gateway ska vidarebefordra trafiken till https://dc.services.visualstudio.com:443

Ersätt värdena ovan med: `http://<your.gateway.address>/<relative path>`
 
Exempel: 
```
http://<your.gateway.endpoint>/v2/track 
http://<your.gateway.endpoint>/api/profiles/{0}/apiId
```




## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan jag köra webbtester för tillgänglighet på en intranätserver?

Vår [webbtester](app-insights-monitor-web-app-availability.md) körs på anslutningspunkter som distribueras över hela världen. Det finns två lösningar:

* Brandväggen dörren – Tillåt att begäranden till servern från [långa och kan ändras över test Webbagenter](app-insights-ip-addresses.md).
* Skriv din egen kod för att skicka regelbundna begäranden till din server i intranätet. Du kan köra Visual Studio-webbtest för detta ändamål. Testaren kunde skicka resultaten till Application Insights med hjälp av TrackAvailability()-API.

## <a name="more-answers"></a>Fler svar
* [Application Insights-forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
