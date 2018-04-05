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
ms.topic: article
ms.date: 04/12/2017
ms.author: mbullwin
ms.openlocfilehash: 245bd348b9eb5b434360d734e219efd7c663a406
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Vanliga frågor och svar

## <a name="configuration-problems"></a>Konfigurationsproblem
*Jag har problem med att ställa min:*

* [.NET-app](app-insights-asp-net-troubleshoot-no-data.md)
* [Övervaka en app redan körs](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Azure-diagnostik](app-insights-azure-diagnostics.md)
* [Java-webbapp](app-insights-java-troubleshoot.md)

*Jag får inga data från servern*

* [Ange undantag för brandväggen](app-insights-ip-addresses.md)
* [Konfigurera en ASP.NET-server](app-insights-monitor-performance-live-website-now.md)
* [Konfigurera en Java-server](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Kan jag använda Application Insights med...?

* [Webbprogram på en IIS-server - lokalt eller i en virtuell dator](app-insights-asp-net.md)
* [Java-webbappar](app-insights-java-get-started.md)
* [Node.js-appar](app-insights-nodejs.md)
* [Web apps i Azure](app-insights-azure-web-apps.md)
* [På Azure-molntjänster](app-insights-cloudservices.md)
* [Appservrar som körs i Docker](app-insights-docker.md)
* [Webbprogram på en sida](app-insights-javascript.md)
* [SharePoint](app-insights-sharepoint.md)
* [Windows-skrivbordsapp](app-insights-windows-desktop.md)
* [Andra plattformar](app-insights-platforms.md)

## <a name="is-it-free"></a>Är det lediga?

Ja, för experiment. I grundläggande prisavtal kan programmet skicka en viss ersättning av data varje månad utan kostnad. Ledigt ersättningen är tillräckligt stor för att omfattar utveckling och publicera en app för ett litet antal användare. Du kan ange ett tak för att förhindra att mer än en angiven mängd data som bearbetas.

Större mängder telemetri debiteras GB. Vi tillhandahåller några tips om hur du [begränsa dina debiteringar](app-insights-pricing.md).

Enterprise-planen har en avgift för varje dag som varje webbservernoden skickar telemetri. Det är lämpligt om du vill använda löpande Export i större skala.

[Läsa prisplanen](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Hur mycket det kostnadshantering?

* Öppna den **användnings- och uppskattade kostnaderna sidan** sida i Application Insights-resurs. Det finns ett diagram för senare användning. Du kan ange en volym fjärrskrivbordsanslutning data om du vill.
* Öppna den [Azure Billing bladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) att se din växlar över alla resurser.

## <a name="q14"></a>Vad Application Insights ändra i projektet?
Informationen beror på vilken typ av projekt. För ett webbprogram:

* Lägger till de här filerna i projektet:

  * ApplicationInsights.config.
  * ai.js
* Installerar dessa NuGet-paket:

  * *Application Insights API* -core API
  * *Application Insights API för webbprogram* – används för att skicka telemetri från servern
  * *Application Insights API för JavaScript-program* – används för att skicka telemetri från klienten

    Paketen är dessa sammansättningar:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Infogar objekt till:

  * Web.config
  * packages.config
* (Nytt projekt endast - om du [lägga till Application Insights till ett befintligt projekt][start], du måste göra det manuellt.) Infogar kodavsnitt i koden för klienten och servern att initiera dem med Application Insights-resurs-ID. Till exempel i en MVC-app infogas kod huvudsida Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hur uppgraderar jag från äldre versioner av SDK?
Finns det [viktig information](app-insights-release-notes.md) för SDK lämpliga för ditt program.

## <a name="update"></a>Hur kan jag ändra vilka Azure-resurs projektet skickar data till?
I Solution Explorer högerklickar du på `ApplicationInsights.config` och välj **uppdatering Application Insights**. Du kan skicka data till en befintlig eller ny resurs i Azure. Uppdateringsguiden ändrar nyckeln instrumentation i ApplicationInsights.config som avgör om servern SDK skickar data. Om du avmarkerar ”uppdatera alla” ändras även nyckeln var det visas på webbsidorna.

## <a name="what-is-status-monitor"></a>Vad är Statusövervakaren?

En skrivbordsapp som du kan använda i din IIS-webbserver för att konfigurera Application Insights i web apps. Den inte samla in telemetri: du kan avbryta den när du inte konfigurerar en app. 

[Läs mer](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Vilka telemetri samlas in av Application Insights?

Från server web apps:

* HTTP-begäranden
* [Beroenden](app-insights-asp-net-dependencies.md). Anrop till: SQL-databaser. HTTP-anrop till externa tjänster. Azure Cosmos DB, tabell, blob-lagring och kön. 
* [Undantag](app-insights-asp-net-exceptions.md) och Stacka spårningar.
* [Prestandaräknare](app-insights-performance-counters.md) - om du använder [statusövervakaren](app-insights-monitor-performance-live-website-now.md), [Azure övervakning](app-insights-azure-web-apps.md) eller [Programinsikter collectd writer](app-insights-java-collectd.md).
* [Anpassade händelser och mått](app-insights-api-custom-events-metrics.md) du code.
* [Spårningsloggar](app-insights-asp-net-trace-logs.md) om du konfigurerar lämpliga insamlaren.

Från [klienten webbsidor](app-insights-javascript.md):

* [Vyn sida räknar](app-insights-web-track-usage.md)
* [AJAX-anrop](app-insights-asp-net-dependencies.md) begäranden som görs från ett skript som körs.
* Läs in data om sidvisningar
* Användar- och antal
* [Autentiserat användar-ID](app-insights-api-custom-events-metrics.md#authenticated-users)

Från andra källor, om du konfigurerar dem:

* [Azure-diagnostik](app-insights-azure-diagnostics.md)
* [Docker-behållare](app-insights-docker.md)
* [Importera tabellerna till Analytics](app-insights-analytics-import.md)
* [Log Analytics](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan jag filtrera bort eller ändra vissa telemetri?

Ja, i den server som du kan skriva:

* Telemetri processorn för att filtrera eller lägga till egenskaper till valda telemetri objekten innan de skickas från din app.
* Telemetri initieraren ska lägga till egenskaper för alla objekt av telemetri.

Lär dig mer om [ASP.NET](app-insights-api-filtering-sampling.md) eller [Java](app-insights-java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Hur beräknas ort, land och andra geo lokaliseringsuppgifter?

Vi söka efter IP-adress (IPv4 eller IPv6) för webbklienten med [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Webbläsaren telemetri: vi samlar in avsändarens IP-adress.
* Servern telemetri: modul i Application Insights samlar in klientens IP-adress. Den har inte samlats in om `X-Forwarded-For` har angetts.

Du kan konfigurera den `ClientIpHeaderTelemetryInitializer` göra IP-adress från ett annat värde. I vissa system, till exempel den flyttas med en proxy, läsa in belastningsutjämnare eller CDN till `X-Originating-IP`. [Läs mer](http://apmtips.com/blog/2016/07/05/client-ip-address/).

Du kan [använda Power BI](app-insights-export-power-bi.md) att visa din begärandetelemetri på en karta.


## <a name="data"></a>Hur länge sparas data i portalen? Är det säkra?
Ta en titt på [datalagring och sekretess][data].

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>Kan personligt identifierbar information (PII) skickas i telemetrin?

Detta är möjligt om koden skickar dessa data. Det kan också inträffa om variabler i stackspår innehåller personligt identifierbar information. Utvecklingsgruppen bör utföra riskbedömningar för att säkerställa att PII hanteras korrekt. [Lär dig mer om datalagring och sekretess](app-insights-data-retention-privacy.md).

**Alla** oktetterna i webbadressen klienten alltid har angetts till 0 när attributen geo plats slås upp.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>Min iKey är synlig i min webbsideskällan. 

* Detta är vanligt i övervakningslösningar.
* Det går inte att användas för att stjäla dina data.
* Den kan användas för att ge skeva aviseringarna data eller utlösare.
* Vi har inte hört att en kund har haft sådana problem.

Du kan:

* Använd två separata iKeys (avgränsa Application Insights-resurser), för klient och server-data. Eller
* Skriva en proxy som körs på servern och har webbklienten skicka data via proxyinställningarna.

## <a name="post"></a>Hur ser postdata i diagnostiska sökningen?
Vi inte logga postdata automatiskt, men du kan använda ett TrackTrace anrop: placera data i parametern meddelandet. Detta har en längre storleksgräns än begränsningar i egenskaperna för anslutningssträngen, men du inte kan filtrera på den.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Bör jag använda en eller flera Application Insights-resurser?

Använda en enskild resurs för alla komponenter eller roller i en enda affärssystem. Använda separata resurser för utveckling och test-versioner och för oberoende program.

* [Finns i avsnittet här](app-insights-separate-resources.md)
* [Exempel - tjänst i molnet med worker och webbtjänst roller](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hur ändrar jag nyckeln instrumentation dynamiskt?

* [Diskussion här](app-insights-separate-resources.md)
* [Exempel - tjänst i molnet med worker och webbtjänst roller](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Vad är användar- och Session räknar?

* JavaScript SDK anger en användare cookie på webbklienten för att identifiera returnerar användare och en sessions-cookie till gruppaktiviteter.
* Om det inte finns några klientskript, kan du [använder cookies på servern](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Om en användare använder webbplatsen i olika webbläsare eller använda i-privat/incognito surfning eller olika datorer och sedan räknas mer än en gång.
* För att identifiera en inloggad användare över datorer och webbläsare kan du lägga till ett anrop till [setAuthenticatedUserContext()](app-insights-api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> Har jag aktiverat allt i Application Insights?
| Vad du bör se | Hur du gör den | Varför du vill |
| --- | --- | --- |
| Tillgänglighet diagram |[Webbtester](app-insights-monitor-web-app-availability.md) |Känner till ditt webbprogram som är igång |
| Server app perf: svarstider,... |[Lägg till Application Insights i ditt projekt](app-insights-asp-net.md) eller [installera AI Status Monitor på servern](app-insights-monitor-performance-live-website-now.md) (eller skriva egen kod till [spåra beroenden](app-insights-api-custom-events-metrics.md#trackdependency)) |Identifiera problem med prestanda |
| Beroendetelemetri |[Installera AI Status Monitor på servern](app-insights-monitor-performance-live-website-now.md) |Diagnostisera problem med databaser eller andra externa komponenter |
| Hämta stackspår från undantag |[Infoga TrackException anrop i koden](app-insights-asp-net-exceptions.md) (men vissa rapporteras automatiskt) |Identifiera och diagnostisera undantag |
| Sök loggspårningar |[Lägg till ett loggning-kort](app-insights-asp-net-trace-logs.md) |Diagnostisera undantag, perf-problem |
| Klienten användning grunderna: sidvisningar, sessioner,... |[JavaScript-initieraren på webbsidor](app-insights-javascript.md) |Användningsanalys |
| Anpassade mått för klienten |[Spårning anropar på webbsidor](app-insights-api-custom-events-metrics.md) |Förbättra användarupplevelsen |
| Anpassade mått för Server |[Spårning av anrop i server](app-insights-api-custom-events-metrics.md) |Business Intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Varför är antal i Sök- och mått olika?

[Provtagning](app-insights-sampling.md) minskar antalet telemetri objekt (begäranden, anpassade händelser och så vidare) som har skickats från din app till portalen. I sökning visas antalet objekt som faktiskt togs emot. I mått diagram som visar antalet händelser kan se du antalet ursprungliga händelser som inträffade. 

Varje objekt som överförs utför en `itemCount` egenskap som visar hur många ursprungliga händelser att objektet representerar. Du kan köra den här frågan i Analytics för att Observera provtagning i åtgärd:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automatisering

### <a name="configuring-application-insights"></a>Konfigurera Application Insights

Du kan [skriva PowerShell-skript](app-insights-powershell.md) med Azure Resursövervakaren till:

* Skapa och uppdatera Application Insights-resurser.
* Ange prisplanen.
* Hämta nyckel för instrumentation.
* Lägga till en avisering om mått.
* Lägg till ett tillgänglighetstest.

Du kan inte definiera ett mått Explorer rapporten eller Ställ in löpande export.

### <a name="querying-the-telemetry"></a>Fråga telemetrin

Använd den [REST API](https://dev.applicationinsights.io/) att köra [Analytics](app-insights-analytics.md) frågor.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Hur ställer jag en avisering på en händelse

Azure-aviseringar är bara för mått. Skapa ett anpassat mått som överskrider ett tröskelvärde för värdet när din händelse inträffar. Ange sedan en avisering för måttet. Observera att: du får ett meddelande om måttet överskrider tröskelvärdet i vardera riktning; Du får inte ett meddelande till den första korsande, oavsett om det initiala värdet är hög eller låg; Det finns alltid en fördröjning på några minuter.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Finns det data transfer avgifter mellan en Azure-webbapp och Application Insights?

* Om din Azure webbapp finns i ett datacenter där det finns en samling Application Insights-slutpunkt, är kostnadsfri. 
* Om det finns inga insamlingsslutpunkten i datacentret värden kommer din app telemetri orsakar [Azure utgående avgifter](https://azure.microsoft.com/pricing/details/bandwidth/).

Detta beroende inte där Application Insights-resursen finns. Det beror på distribution av våra slutpunkter.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan jag skicka telemetri till Application Insights-portalen?

Vi rekommenderar att du använder våra SDK: er och använder den [SDK API](app-insights-api-custom-events-metrics.md). Det finns olika varianter av SDK för olika [plattformar](app-insights-platforms.md). Dessa SDK hantera buffring, komprimering, begränsning, återförsök och så vidare. Dock den [införandet schemat](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) och [endpoint protokollet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) är offentlig.

## <a name="can-i-monitor-an-intranet-web-server"></a>Övervakar jag en intranät-webbserver?

Här följer två metoder:

### <a name="firewall-door"></a>Dörren för brandvägg

Tillåt att webbservern ska skicka telemetri till vår slutpunkter https://dc.services.visualstudio.com:443 och https://rt.services.visualstudio.com:443. 

### <a name="proxy"></a>Proxy

Vidarebefordra trafik från servern till en gateway på intranätet, genom att ange detta i ApplicationInsights.config:

```XML
<TelemetryChannel>
    <EndpointAddress>your gateway endpoint</EndpointAddress>
</TelemetryChannel>
```

Din gateway ska vidarebefordra trafiken till https://dc.services.visualstudio.com:443/v2/track

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan jag köra webbtester för tillgänglighet på en server för intranät?

Vår [webbtester](app-insights-monitor-web-app-availability.md) körs på punkterna i förekomst som är utspridda över hela världen. Det finns två lösningar:

* Brandväggen dörren – Tillåt att begäranden till servern från [långa och går att ändra listan över test Webbagenter](app-insights-ip-addresses.md).
* Skriv koden för att skicka periodiska begäranden till servern från i intranätet. Du kan köra Visual Studio-webbtest för detta ändamål. Testaren kan skicka resultaten till Application Insights med TrackAvailability()-API.

## <a name="more-answers"></a>Fler svar
* [Application Insights-forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
