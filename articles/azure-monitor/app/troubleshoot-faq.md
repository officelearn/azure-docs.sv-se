---
title: Vanliga frågor och svar om Azure Application Insights | Microsoft Docs
description: Vanliga frågor och svar om Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: mbullwin
ms.openlocfilehash: 94e994a3dc1cd9d5d5d0b7acb5aed4783d881915
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802290"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Vanliga frågor och svar

## <a name="configuration-problems"></a>Konfigurations problem
*Jag har problem med att konfigurera mitt:*

* [.NET-app](asp-net-troubleshoot-no-data.md)
* [Övervaka en app som redan körs](monitor-performance-live-website-now.md#troubleshoot)
* [Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Java-webbapp](java-troubleshoot.md)

*Jag får inga data från min server*

* [Ange brand Väggs undantag](ip-addresses.md)
* [Konfigurera en ASP.NET-Server](monitor-performance-live-website-now.md)
* [Konfigurera en Java-Server](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Kan jag använda Application Insights med...?

* [Webbappar på en IIS-server på en virtuell Azure-dator eller skalnings uppsättning för virtuella Azure-datorer](azure-vm-vmss-apps.md)
* [Web Apps på en IIS-server – lokalt eller i en virtuell dator](asp-net.md)
* [Java-webbappar](java-get-started.md)
* [Node.js-appar](nodejs.md)
* [Web Apps på Azure](azure-web-apps.md)
* [Cloud Services på Azure](cloudservices.md)
* [App-servrar som körs i Docker](docker.md)
* [Webb program med en enda sida](javascript.md)
* [SharePoint](sharepoint.md)
* [Windows Desktop-app](windows-desktop.md)
* [Andra plattformar](platforms.md)

## <a name="is-it-free"></a>Är det kostnads fritt?

Ja, för experimentell användning. I den grundläggande pris Planen kan ditt program skicka en viss justering av data varje månad utan kostnad. Den kostnads fria tilldelningen är tillräckligt stor för att kunna utveckla och publicera en app för ett litet antal användare. Du kan ange en Cap för att förhindra att fler än en angiven mängd data bearbetas.

Större telemetri debiteras av GB. Vi ger några tips om hur du [begränsar dina avgifter](pricing.md).

Företags planen debiteras en avgift för varje dag som varje nod i webb servern skickar telemetri. Det är lämpligt om du vill använda kontinuerlig export i stor skala.

[Läs pris Planen](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-does-it-cost"></a>Hur mycket kostar det?

* Öppna **sidan användning och uppskattade kostnader** i en Application Insights resurs. Det finns ett diagram över senaste användningen. Du kan ange en data volyms gräns om du vill.
* Öppna [Azures fakturerings blad](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) för att se dina fakturor för alla resurser.

## <a name="q14"></a>Vad kan Application Insights ändra i mitt projekt?
Informationen beror på projekt typen. För ett webb program:

* Lägger till de här filerna i projektet:

  * ApplicationInsights.config.
  * ai.js
* Installerar dessa NuGet-paket:

  * *Application Insights API* – Core API
  * *Application Insights-API för webb program* – används för att skicka telemetri från servern
  * *Application Insights API för JavaScript-program* – används för att skicka telemetri från klienten

    Paketen innehåller följande sammansättningar:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Infogar objekt i:

  * Web.config
  * packages.config
* (Endast nya projekt – om du [lägger till Application Insights i ett befintligt projekt][start]måste du göra det manuellt.) Infogar kodfragment i klient-och Server koden för att initiera dem med Application Insights resurs-ID. I en MVC-app infogas till exempel kod i vyerna för huvud sidan/Shared/_Layout. cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hur gör jag för att uppgradering från äldre SDK-versioner?
Se [viktig information](release-notes.md) om SDK: n som passar din typ av program.

## <a name="update"></a>Hur kan jag ändra vilken Azure-resurs mitt projekt skickar data till?
I Solution Explorer högerklickar du på `ApplicationInsights.config` och väljer **Uppdatera Application Insights**. Du kan skicka data till en befintlig eller ny resurs i Azure. Uppdaterings guiden ändrar Instrumentation-nyckeln i ApplicationInsights. config, som avgör var serverns SDK skickar dina data. Om du inte avmarkerar kryss rutan uppdatera alla, ändras även nyckeln där den visas på dina webb sidor.

## <a name="what-is-status-monitor"></a>Vad är Statusövervakaren?

En Skriv bords app som du kan använda i IIS-webbservern för att konfigurera Application Insights i Web Apps. Den samlar inte in telemetri: du kan stoppa det när du inte konfigurerar en app. 

[Läs mer](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Vilken telemetri samlas in av Application Insights?

Från Server Web Apps:

* HTTP-begäranden
* [Beroenden](asp-net-dependencies.md). Anrop till: SQL-databaser; HTTP-anrop till externa tjänster; Azure Cosmos DB, Table, Blob Storage och Queue. 
* [Undantag](asp-net-exceptions.md) och stack spår.
* [Prestanda räknare](performance-counters.md) – om du använder [statusövervakare](monitor-performance-live-website-now.md), [Azure-övervakning för app Services](azure-web-apps.md), [Azure-övervakning för virtuell dator eller skalnings uppsättning för virtuella datorer](azure-vm-vmss-apps.md), eller [Application Insights insamlad skrivare](java-collectd.md).
* [Anpassade händelser och mått](api-custom-events-metrics.md) som du kodar.
* [Spårnings loggar](asp-net-trace-logs.md) om du konfigurerar lämplig insamlare.

Från [klient webb sidor](javascript.md):

* [Antal sid visningar](usage-overview.md)
* [AJAX-anrop](asp-net-dependencies.md) Begär Anden som görs från ett skript som körs.
* Läsa in data för sid visning
* Antal användare och sessioner
* [Autentiserade användar-ID: n](api-custom-events-metrics.md#authenticated-users)

Från andra källor, om du konfigurerar dem:

* [Azure Diagnostics](../platform/diagnostics-extension-to-application-insights.md)
* [Importera till analys](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan jag filtrera ut eller ändra vissa telemetri?

Ja, på den server som du kan skriva:

* Telemetri-processor för att filtrera eller lägga till egenskaper för valda telemetridata innan de skickas från din app.
* Telemetri-initierare för att lägga till egenskaper till alla objekt i telemetri.

Läs mer om [ASP.net](api-filtering-sampling.md) eller [Java](java-filter-telemetry.md).

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hur beräknas stad, land/region och annan Geo-plats data?

Vi letar upp IP-adressen (IPv4 eller IPv6) för webb klienten med hjälp av [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Webb läsar telemetri: Vi samlar in avsändarens IP-adress.
* Server telemetri: Application Insights-modulen samlar in klientens IP-adress. Den samlas inte in om `X-Forwarded-For` har angetts.
* Mer information om hur IP-adress och data för geolokalisering samlas in i Application Insights finns i den här [artikeln](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Du kan konfigurera `ClientIpHeaderTelemetryInitializer` att ta med IP-adressen från en annan rubrik. I vissa system flyttas den till exempel av en proxy, belastningsutjämnare eller CDN till `X-Originating-IP`. [Läs mer](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Du kan [använda Power BI](export-power-bi.md ) för att visa din begär ande telemetri på en karta.


## <a name="data"></a>Hur länge sparas data i portalen? Är det säkert?
Ta en titt på [data kvarhållning och sekretess][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kan personliga data skickas i telemetri?

Detta är möjligt om din kod skickar sådana data. Det kan också hända om variabler i stack spår innehåller person uppgifter. Utvecklings gruppen bör genomföra riskbedömningar för att säkerställa att person uppgifter hanteras korrekt. [Läs mer om data kvarhållning och sekretess](data-retention-privacy.md).

**Alla** oktetter i klient webb adressen är alltid inställda på 0 när attributen för geo-platsen har sökts upp.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Min Instrumentation-nyckel är synlig i min webb Side källa. 

* Det här är en vanlig metod för övervakning av lösningar.
* Det kan inte användas för att stjäla dina data.
* Det kan användas för att skeva data eller utlösa aviseringar.
* Vi har inte hört att någon kund har haft sådana problem.

Du kan:

* Använd två separata instrument knappar (separata Application Insights-resurser) för klient-och Server data. Eller
* Skriv en proxy som körs på servern och låt webb klienten skicka data via den proxyservern.

## <a name="post"></a>Hur gör jag för att se skicka data i diagnostisk sökning?
Vi loggar inte in POST-data automatiskt, men du kan använda ett TrackTrace-anrop: Lägg till data i parametern Message. Det finns en längre storleks gräns än gränserna för sträng egenskaper, men det går inte att filtrera på den.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Bör jag använda en eller flera Application Insights resurser?

Använd en enda resurs för alla komponenter eller roller i ett enda företags system. Använd separata resurser för utveckling, test och versions versioner och för oberoende program.

* [Se diskussionen här](separate-resources.md)
* [Exempel – moln tjänst med Worker-och Web-roller](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hur gör jag för att du ändra Instrumentation-nyckeln dynamiskt?

* [Diskussion här](separate-resources.md)
* [Exempel – moln tjänst med Worker-och Web-roller](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Vad är antalet användare och sessioner?

* Java Script SDK anger en användar-cookie på webb klienten, för att identifiera användare och en sessions-cookie för att gruppera aktiviteter.
* Om det inte finns något skript på klient sidan kan du [Ange cookies på servern](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Om en riktig användare använder din webbplats i olika webbläsare, eller använder privat/Incognito-surfning eller olika datorer, kommer de att räknas mer än en gång.
* Om du vill identifiera en inloggad användare på datorer och webbläsare lägger du till ett anrop till [setAuthenticatedUserContext ()](api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a>Har jag aktiverat allt i Application Insights?
| Vad du bör se | Så här hämtar du det | Varför du vill ha den |
| --- | --- | --- |
| Tillgänglighets diagram |[Webbtester](monitor-web-app-availability.md) |Vet att din webbapp är igång |
| Server App-prestanda: svars tider,... |[Lägg till Application Insights i projektet](asp-net.md) eller [installera AI-statusövervakare på servern](monitor-performance-live-website-now.md) (eller Skriv din egen kod för att [spåra beroenden](api-custom-events-metrics.md#trackdependency)) |Identifiera prestanda problem |
| Beroende telemetri |[Installera AI-Statusövervakare på servern](monitor-performance-live-website-now.md) |Diagnostisera problem med databaser eller andra externa komponenter |
| Hämta stack spår från undantag |[Infoga TrackException-anrop i din kod](asp-net-exceptions.md) (men vissa rapporteras automatiskt) |Identifiera och diagnostisera undantag |
| Sök logg spårningar |[Lägg till ett loggnings kort](asp-net-trace-logs.md) |Diagnostisera undantag, prestanda problem |
| Grundläggande om klient användning: sidvyer, sessioner,... |[JavaScript-initierare på webb sidor](javascript.md) |Användninganalys |
| Anpassade mått för klienter |[Spåra samtal på webb sidor](api-custom-events-metrics.md) |Förbättrad användarupplevelse |
| Anpassade mått för Server |[Spåra anrop i Server](api-custom-events-metrics.md) |Business Intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Varför är antalet i Sök-och mått diagram desamma?

[Sampling](sampling.md) minskar antalet telemetridata (begär Anden, anpassade händelser osv.) som faktiskt skickas från din app till portalen. I sökningen visas antalet objekt som faktiskt har tagits emot. I mått diagram som visar antalet händelser visas antalet ursprungliga händelser som har inträffat. 

Varje objekt som överförs har en `itemCount` egenskap som visar hur många ursprungliga händelser som objektet representerar. Du kan köra den här frågan i Analytics för att se hur prover fungerar:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Konfigurera Application Insights

Du kan [skriva PowerShell-skript](powershell.md) med hjälp av Azure resursövervakare för att:

* Skapa och uppdatera Application Insights-resurser.
* Ange pris planen.
* Hämta Instrumentation-nyckeln.
* Lägg till en måtta varning.
* Lägg till ett tillgänglighets test.

Du kan inte konfigurera en Metric Explorer-rapport eller konfigurera löpande export.

### <a name="querying-the-telemetry"></a>Fråga telemetri

Använd [REST API](https://dev.applicationinsights.io/) för att köra [analys](analytics.md) frågor.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Hur kan jag ange en avisering för en händelse?

Azure-aviseringar är bara på mått. Skapa ett anpassat mått som korsar ett värde tröskelvärde när händelsen inträffar. Ange sedan en avisering för måttet. Observera att: du får ett meddelande när måttet korsar tröskelvärdet i båda riktningarna. du får inget meddelande förrän den första korsningen, oavsett om det ursprungliga värdet är högt eller lågt. Det finns alltid en fördröjning på några minuter.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Kostar data överföring mellan en Azure-webbapp och Application Insights?

* Om din Azure-webbapp finns i ett Data Center där det finns en Application Insights samlings slut punkt finns det ingen kostnad. 
* Om det inte finns någon samlings slut punkt i värd data centret kommer din apps telemetri att debiteras [utgående Azure-avgifter](https://azure.microsoft.com/pricing/details/bandwidth/).

Detta är inte beroende av var Application Insights resursen finns. Det beror bara på distributionen av våra slut punkter.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan jag skicka telemetri till Application Insights-portalen?

Vi rekommenderar att du använder våra SDK [: er](api-custom-events-metrics.md)och använder SDK-API: et. Det finns varianter av SDK för olika [plattformar](platforms.md). Dessa SDK: er hanterar buffring, komprimering, begränsning, nya försök och så vidare. Men inmatnings [schema](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) och [slut punkts protokoll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) är offentliga.

## <a name="can-i-monitor-an-intranet-web-server"></a>Kan jag övervaka en intranät webb server?

Ja, men du måste tillåta trafik till våra tjänster genom brand Väggs undantag eller omdirigering av proxy.
- QuickPulse`https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider`https://dc.services.visualstudio.com:443` 
- TelemetryChannel`https://dc.services.visualstudio.com:443` 


Läs vår fullständiga lista över tjänster och IP-adresser [här](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Brand Väggs undantag

Tillåt att din webb server skickar telemetri till våra slut punkter. 

### <a name="gateway-redirect"></a>Omdirigera Gateway

Dirigera trafik från servern till en gateway på intranätet genom att skriva över slut punkter i konfigurationen.
Om dessa "slut punkts egenskaper" inte finns i konfigurationen, använder dessa klasser standardvärdena som visas nedan i exemplet ApplicationInsights. config. 

Din gateway ska dirigera trafik till vår slut punkts bas adress. Ersätt standardvärdena i konfigurationen med `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exempel på ApplicationInsights. config med standard slut punkter:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
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

_Obs! ApplicationIdProvider är tillgänglig från och med v 2.6.0_

### <a name="proxy-passthrough"></a>Proxy-genomströmning

Du kan åstadkomma proxy genom att konfigurera antingen en proxy på dator nivå eller på program nivå.
Mer information finns i dotNet-artikeln på [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Exempel Web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan jag köra tillgänglighets webb test på en intranät Server?

Våra [](monitor-web-app-availability.md) webbtester körs på närvaro punkter som distribueras världen över. Det finns två lösningar:

* Brand Väggs dörr – Tillåt förfrågningar till servern från [den långa och den ändrings bara listan med webb test agenter](ip-addresses.md).
* Skriv din egen kod för att skicka periodiska förfrågningar till servern inifrån intranätet. Du kan köra Visual Studio-webbtester för detta ändamål. Testaren kan skicka resultatet till Application Insights med hjälp av TrackAvailability ()-API: et.

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Hur lång tid tar det för telemetri att samlas in?

De flesta Application Insights data har en fördröjning på under 5 minuter. Vissa data kan ta längre tid. oftast större loggfiler. Mer information finns i [service avtalet för Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Fler svar
* [Application Insights forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
