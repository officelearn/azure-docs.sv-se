---
title: Azure Monitor vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor om Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/23/2020
ms.openlocfilehash: 169cc3f6a195a7339ab33eb9f00d9937451a93e0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275586"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Monitor

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Monitor.

## <a name="general"></a>Allmänt

### <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
[Azure Monitor](overview.md) är en tjänst i Azure som tillhandahåller prestanda-och tillgänglighets övervakning för program och tjänster i Azure, andra moln miljöer eller lokalt. Azure Monitor samlar in data från flera källor i en gemensam data plattform där de kan analyseras för trender och avvikelser. Omfattande funktioner i Azure Monitor hjälper dig att snabbt identifiera och reagera på kritiska situationer som kan påverka ditt program.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Vad är skillnaden mellan Azure Monitor, Log Analytics och Application Insights?
I september 2018 har Microsoft kombinerat Azure Monitor, Log Analytics och Application Insights till en enda tjänst för att tillhandahålla kraftfull övervakning av programmen och de komponenter de förlitar sig på. Funktioner i Log Analytics och Application Insights har inte ändrats, men vissa funktioner har ändrats till Azure Monitor för att bättre återspegla sitt nya omfång. Logg data motorn och frågespråket för Log Analytics kallas nu Azure Monitor loggar. Se [Azure Monitor terminologi-uppdateringar](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Vad kostar Azure Monitor?
Funktioner i Azure Monitor som aktive ras automatiskt, till exempel insamling av mått och aktivitets loggar får utan kostnad. Det finns en kostnad som är kopplad till andra funktioner som logg frågor och aviseringar. Se [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/) för detaljerad pris information.

### <a name="how-do-i-enable-azure-monitor"></a>Hur gör jag för att aktivera Azure Monitor?
Azure Monitor aktive ras så snart du skapar en ny Azure-prenumeration, och [aktivitets logg](platform/activity-logs-overview.md) -och plattforms [mått](platform/data-platform-metrics.md) samlas in automatiskt. Skapa [diagnostikinställningar](platform/diagnostic-settings.md) för att samla in mer detaljerad information om driften av dina Azure-resurser och lägga till [övervaknings lösningar](insights/solutions.md) och [insikter](insights/insights-overview.md) för att tillhandahålla ytterligare analys av insamlade data för specifika tjänster. 

### <a name="how-do-i-access-azure-monitor"></a>Hur gör jag för att åtkomst Azure Monitor?
Få åtkomst till alla Azure Monitor funktioner och data från **Monitor** -menyn i Azure Portal. I avsnittet **övervakning** på menyn för olika Azure-tjänster får du till gång till samma verktyg med data som har filtrerats till en viss resurs. Azure Monitor data är också tillgängliga för en mängd olika scenarier med CLI, PowerShell och en REST API.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Finns det en lokal version av Azure Monitor?
Nej. Azure Monitor är en skalbar moln tjänst som bearbetar och lagrar stora mängder data, även om Azure Monitor kan övervaka resurser som är lokala och i andra moln.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Kan Azure Monitor övervaka lokala resurser?
Ja, förutom att samla in övervaknings data från Azure-resurser kan Azure Monitor samla in data från virtuella datorer och program i andra moln och lokalt. Se [källor för övervaknings data för Azure Monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Är Azure Monitor integrera med System Center Operations Manager?
Du kan ansluta din befintliga System Center Operations Manager hanterings grupp till Azure Monitor för att samla in data från agenter till Azure Monitor loggar. På så sätt kan du använda logg frågor och lösning för att analysera data som samlas in från agenter. Du kan också konfigurera befintliga System Center Operations Managers agenter att skicka data direkt till Azure Monitor. Se [ansluta Operations Manager till Azure Monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Vilka IP-adresser använder Azure Monitor?
Se [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) för en lista över IP-adresser och portar som krävs för agenter och andra externa resurser för att få åtkomst till Azure Monitor. 

## <a name="monitoring-data"></a>Övervaka data

### <a name="where-does-azure-monitor-get-its-data"></a>Var kan Azure Monitor hämta sina data?
Azure Monitor samlar in data från en mängd olika källor, inklusive loggar och mått från Azure-plattform och resurser, anpassade program och agenter som körs på virtuella datorer. Andra tjänster som Azure Security Center och Network Watcher samla in data i en Log Analytics arbets yta så att den kan analyseras med Azure Monitor data. Du kan också skicka anpassade data till Azure Monitor med hjälp av REST API för loggar eller mått. Se [källor för övervaknings data för Azure Monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Vilka data samlas in av Azure Monitor? 
Azure Monitor samlar in data från en mängd olika källor i [loggar](platform/data-platform-logs.md) eller [mått](platform/data-platform-metrics.md). Varje typ av data har sina egna relativa fördelar och var och en har stöd för en viss uppsättning funktioner i Azure Monitor. Det finns en enda mått databas för varje Azure-prenumeration, medan du kan skapa flera Log Analytics arbets ytor för att samla in loggar beroende på dina behov. Se [Azure Monitor data plattform](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Finns det en maximal mängd data som jag kan samla in i Azure Monitor?
Det finns ingen gräns för mängden Mät data som du kan samla in, men dessa data lagras i högst 93 dagar. Se [kvarhållning av mått](platform/data-platform-metrics.md#retention-of-metrics). Det finns ingen gräns för mängden logg data som du kan samla in, men det kan påverkas av den pris nivå som du väljer för arbets ytan Log Analytics. Se [pris information](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Hur gör jag för att åtkomst till data som samlats in av Azure Monitor?
Insikter och lösningar ger en anpassad upplevelse för att arbeta med data som lagras i Azure Monitor. Du kan arbeta direkt med loggdata med hjälp av en logg fråga som skrivits i KQL (Kusto Query Language). I Azure Portal kan du skriva och köra frågor och analysera data interaktivt med hjälp av Log Analytics. Analysera mått i Azure Portal med Metrics Explorer. Se [analysera loggdata i Azure Monitor](log-query/log-query-overview.md) och [komma igång med Azure Metrics Explorer](platform/metrics-getting-started.md).





## <a name="solutions-and-insights"></a>Lösningar och insikter

### <a name="what-is-an-insight-in-azure-monitor"></a>Vad är insikter i Azure Monitor?
Insikter ger en anpassad övervaknings upplevelse för specifika Azure-tjänster. De använder samma mått och loggar som andra funktioner i Azure Monitor men kan samla in ytterligare data och ge en unik upplevelse i Azure Portal. Se [insikter i Azure Monitor](insights/insights-overview.md).

Information om hur du visar insikter i Azure Portal finns i avsnittet om **insikter** på **Monitor** -menyn eller i avsnittet **övervakning** på tjänstens meny.

### <a name="what-is-a-solution-in-azure-monitor"></a>Vad är en lösning i Azure Monitor?
Övervaknings lösningar är paketerade uppsättningar av logik för att övervaka ett visst program eller en viss tjänst baserat på Azure Monitor funktioner. De samlar in loggdata i Azure Monitor och tillhandahåller logg frågor och vyer för analys av dem med hjälp av en vanlig upplevelse i Azure Portal. Se [övervaknings lösningar i Azure Monitor](insights/solutions.md).

Om du vill visa lösningar i Azure Portal klickar du på **mer** i avsnittet om **insikter** på **Monitor** -menyn. Klicka på **Lägg** till för att lägga till ytterligare lösningar på arbets ytan.






## <a name="logs"></a>Loggar

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Vad är skillnaden mellan Azure Monitor loggar och Azure Datautforskaren?
Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Monitor-loggar bygger på Azure-Datautforskaren och använder samma KQL (Kusto Query Language) med vissa mindre skillnader. Se [skillnader i Azure Monitor loggs frågespråk](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Hur gör jag för att hämta logg data?
Alla data hämtas från en Log Analytics-arbetsyta med hjälp av en logg fråga som skrivits med hjälp av KQL (Kusto Query Language). Du kan skriva egna frågor eller använda lösningar och insikter som innehåller logg frågor för ett visst program eller en viss tjänst. Se [Översikt över logg frågor i Azure Monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Vad är en Log Analytics arbets yta?
Alla loggdata som samlas in av Azure Monitor lagras i en Log Analytics arbets yta. En arbets yta är i grunden en behållare där loggdata samlas in från olika källor. Du kan ha en enda Log Analytics arbets yta för alla dina övervaknings data eller så kan det finnas krav för flera arbets ytor. Se [utforma distributioner av Azure Monitor loggar](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Kan du flytta en befintlig Log Analytics arbets yta till en annan Azure-prenumeration?
Du kan flytta en arbets yta mellan resurs grupper eller prenumerationer, men inte till en annan region. Se [flytta en Log Analytics arbets yta till en annan prenumeration eller resurs grupp](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Varför kan jag inte se Query Explorer och spara knappar i Log Analytics?

Knapparna i **query Explorer**, **Spara** och **ny varnings regel** är inte tillgängliga när [fråge omfånget](log-query/scope.md) är inställt på en enskild resurs. Om du vill skapa aviseringar, Spara eller läsa in en fråga måste Log Analytics begränsas till en arbets yta. Öppna Log Analytics i kontexten för arbets ytan genom att välja **loggar** på **Azure Monitor** menyn. Senaste använda arbetsytan har valts, men du kan välja en annan arbetsyta. Se [omfång och tidsintervall för logg frågor i Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Varför visas följande fel meddelande: "Registrera Resource providern Microsoft. Insights för den här prenumerationen om du vill aktivera den här frågan" när du öppnar Log Analytics från en virtuell dator? 
Många resurs leverantörer registreras automatiskt, men du kan behöva registrera vissa resurs leverantörer manuellt. Omfattningen för registrering är alltid prenumerationen. Mer information finns i [Resursproviders och resurstyper](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Varför får jag inget åtkomst fel meddelande när jag öppnar Log Analytics från en virtuell dator? 
Om du vill visa VM-loggar måste du beviljas med Läs behörighet till de arbets ytor som lagrar de virtuella dator loggarna. I dessa fall kan måste administratören bevilja dig behörigheter i Azure.




## <a name="alerts"></a>Aviseringar

### <a name="what-is-an-alert-in-azure-monitor"></a>Vad är en avisering i Azure Monitor?
Aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Det finns flera typer av aviseringar:

- Metric-Metric-värdet överskrider ett tröskelvärde.
- Logg fråga – resultat av en logg fråga matchar definierade villkor.
- Aktivitets logg-aktivitets logg händelse matchar definierade villkor.
- Webb test – resultat av tillgänglighets test matchar definierade kriterier.


Se [Översikt över aviseringar i Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Vad är en åtgärds grupp?
En åtgärds grupp är en samling meddelanden och åtgärder som kan utlösas av en avisering. Flera aviseringar kan använda en enda åtgärds grupp så att du kan använda vanliga uppsättningar meddelanden och åtgärder. Se [skapa och hantera åtgärds grupper i Azure Portal](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Vad är en åtgärds regel?
Med en åtgärds regel kan du ändra beteendet för en uppsättning aviseringar som matchar ett visst villkor. På så sätt kan du utföra sådana krav som att inaktivera aviserings åtgärder under en underhålls period. Du kan också använda en åtgärds grupp till en uppsättning aviseringar i stället för att använda dem direkt i varnings reglerna. Se [Åtgärds regler](platform/alerts-action-rules.md).


## <a name="agents"></a>Agenter

### <a name="does-azure-monitor-require-an-agent"></a>Kräver Azure Monitor en agent?
En agent krävs bara för att samla in data från operativ systemet och arbets belastningarna på virtuella datorer. De virtuella datorerna kan finnas i Azure, en annan moln miljö eller lokalt. Se [Översikt över Azure Monitors agenter](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Vad är skillnaden mellan Azure Monitors agenter?
Azure Diagnostic Extension är för Azure Virtual Machines och samlar in data för att Azure Monitor Mät värden, Azure Storage och Azure Event Hubs. Log Analytics agenten är för virtuella datorer i Azure, en annan moln miljö eller lokalt och samlar in data till Azure Monitor loggar. Beroende agenten kräver Log Analytics agent och insamlad process information och beroenden. Se [Översikt över Azure Monitors agenter](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Använder min agent trafik min ExpressRoute-anslutning?
Trafik till Azure Monitor använder Microsoft peering ExpressRoute-kretsen. Se [ExpressRoute-dokumentationen](../expressroute/expressroute-faqs.md#supported-services) för en beskrivning av de olika typerna av ExpressRoute-trafik. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Hur kan jag bekräfta att Log Analytics-agenten kan kommunicera med Azure Monitor?
Från kontroll panelen på agent datorn väljer du **säkerhets & inställningar**, **Microsoft Monitoring Agent** . På fliken **Azure Log Analytics (OMS)** bekräftar en grön kryss markerings ikon att agenten kan kommunicera med Azure Monitor. En gul varnings ikon innebär att agenten har problem. En vanlig orsak är att **Microsoft Monitoring Agent** -tjänsten har stoppats. Använda tjänsthanteraren för att starta om tjänsten.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Hur gör jag för att stoppar du Log Analyticss agenten från att kommunicera med Azure Monitor?
För agenter som är anslutna till Log Analytics direkt öppnar du kontroll panelen och väljer **säkerhets & inställningar**, **Microsoft Monitoring Agent**. Ta bort alla arbets ytor som visas under fliken **Azure Log Analytics (OMS)** . I System Center Operations Manager tar du bort datorn från listan Log Analytics hanterade datorer. Operations Manager uppdaterar konfigurationen av agenten inte längre rapporten till Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Hur mycket data skickas per agent?
Mängden data som skickats per agent beror på:

* Lösningar som du har aktiverat
* Antalet loggar och prestandaräknare som samlas in
* Mängden data i loggarna

Mer information finns i [Hantera användning och kostnader med Azure Monitor loggar](platform/manage-cost-storage.md) .

Använd följande fråga för datorer som kommer köras WireData-agenten kan se hur mycket data som skickas:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Hur mycket nätverks bandbredd används av Microsofts hanterings agent (MMA) när du skickar data till Azure Monitor?
Bandbredd är en funktion för mängden data som skickas. Data komprimeras som skickas över nätverket.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Hur kan jag få ett meddelande när data insamlingen från den Log Analytics agenten stoppas?

Använd stegen som beskrivs i [skapa en ny logg avisering](platform/alerts-metric.md) som ska meddelas när data insamlingen stoppas. Använd följande inställningar för aviserings regeln:

- **Definiera aviserings villkor**: Ange Log Analytics arbets ytan som resurs mål.
- **Aviserings villkor** 
   - **Signal namn**: *anpassad loggs ökning*
   - **Sök fråga**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Aviserings logik**: **baserat på** *antalet resultat*, **villkoret** är *större än*, **tröskelvärdet** *0*
   - **Utvärderad baserat på**: **period (i minuter)** *30*, **frekvens (i minuter)** *10*
- **Definiera aviserings information** 
   - **Namn**: *data insamlingen stoppades*
   - **Allvarlighets grad**: *Varning*

Ange en befintlig eller ny [Åtgärds grupp](platform/action-groups.md) så att du får ett meddelande om att ett pulsslag saknas under 15 minuter när logg aviseringen matchar kriterierna.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Vilka är brand Väggs kraven för Azure Monitors agenter?
Se [krav för nätverks brand väggen](platform/log-analytics-agent.md#network-firewall-requirements)för information om brand Väggs krav.


## <a name="visualizations"></a>Visualiseringar

### <a name="why-cant-i-cant-see-view-designer"></a>Varför kan jag inte se View Designer?

View Designer är bara tillgängligt för användare som har tilldelats deltagar behörigheter eller högre i Log Analytics-arbetsytan.


## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Konfigurations problem
*Jag har problem med att konfigurera mitt:*

* [.NET-app](app/asp-net-troubleshoot-no-data.md)
* [Övervaka en app som redan körs](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure Diagnostics](platform/diagnostics-extension-to-application-insights.md)
* [Java-webbapp](app/java-troubleshoot.md)

*Jag får inga data från min server*

* [Ange brand Väggs undantag](app/ip-addresses.md)
* [Konfigurera en ASP.NET-Server](app/monitor-performance-live-website-now.md)
* [Konfigurera en Java-Server](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Kan jag använda Application Insights med...?

* [Webbappar på en IIS-server på en virtuell Azure-dator eller skalnings uppsättning för virtuella Azure-datorer](app/azure-vm-vmss-apps.md)
* [Web Apps på en IIS-server – lokalt eller i en virtuell dator](app/asp-net.md)
* [Java-webbappar](app/java-get-started.md)
* [Node.js-appar](app/nodejs.md)
* [Web Apps på Azure](app/azure-web-apps.md)
* [Cloud Services på Azure](app/cloudservices.md)
* [App-servrar som körs i Docker](app/docker.md)
* [Webb program med en enda sida](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows Desktop-app](app/windows-desktop.md)
* [Andra plattformar](app/platforms.md)

### <a name="is-it-free"></a>Är det kostnads fritt?

Ja, för experimentell användning. I den grundläggande pris Planen kan ditt program skicka en viss justering av data varje månad utan kostnad. Den kostnads fria tilldelningen är tillräckligt stor för att kunna utveckla och publicera en app för ett litet antal användare. Du kan ange en Cap för att förhindra att fler än en angiven mängd data bearbetas.

Större telemetri debiteras av GB. Vi ger några tips om hur du [begränsar dina avgifter](app/pricing.md).

Företags planen debiteras en avgift för varje dag som varje nod i webb servern skickar telemetri. Det är lämpligt om du vill använda kontinuerlig export i stor skala.

[Läs pris Planen](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Hur mycket kostar det?

* Öppna **sidan användning och uppskattade kostnader** i en Application Insights resurs. Det finns ett diagram över senaste användningen. Du kan ange en data volyms gräns om du vill.
* Öppna [Azures fakturerings blad](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) för att se dina fakturor för alla resurser.

### <a name="q14"></a>Vad kan Application Insights ändra i mitt projekt?
Informationen beror på projekt typen. För ett webb program:

* Lägger till de här filerna i projektet:
  * ApplicationInsights.config
  * ai.js
* Installerar dessa NuGet-paket:
  * *Application Insights API* – Core API
  * *Application Insights-API för webb program* – används för att skicka telemetri från servern
  * *Application Insights API för JavaScript-program* – används för att skicka telemetri från klienten
* Paketen innehåller följande sammansättningar:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Infogar objekt i:
  * Web.config
  * packages.config
* (Endast nya projekt – om du [lägger till Application Insights i ett befintligt projekt][start]måste du göra det manuellt.) Infogar kodfragment i klient-och Server koden för att initiera dem med Application Insights resurs-ID. I en MVC-app infogas till exempel kod i vyerna för huvud sidan/Shared/\_layout. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hur gör jag för att uppgradering från äldre SDK-versioner?
Se [viktig information](app/release-notes.md) om SDK: n som passar din typ av program.

### <a name="update"></a>Hur kan jag ändra vilken Azure-resurs mitt projekt skickar data till?
I Solution Explorer högerklickar du på `ApplicationInsights.config` och väljer **uppdatera Application Insights**. Du kan skicka data till en befintlig eller ny resurs i Azure. Uppdaterings guiden ändrar Instrumentation-nyckeln i ApplicationInsights. config, som avgör var serverns SDK skickar dina data. Om du inte avmarkerar kryss rutan uppdatera alla, ändras även nyckeln där den visas på dina webb sidor.

### <a name="what-is-status-monitor"></a>Vad är Statusövervakaren?

En Skriv bords app som du kan använda i IIS-webbservern för att konfigurera Application Insights i Web Apps. Den samlar inte in telemetri: du kan stoppa det när du inte konfigurerar en app. 

[Läs mer](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Vilken telemetri samlas in av Application Insights?

Från Server Web Apps:

* HTTP-begäranden
* [Beroenden](app/asp-net-dependencies.md). Anrop till: SQL-databaser; HTTP-anrop till externa tjänster; Azure Cosmos DB, Table, Blob Storage och Queue. 
* [Undantag](app/asp-net-exceptions.md) och stack spår.
* [Prestanda räknare](app/performance-counters.md) – om du använder [statusövervakare](app/monitor-performance-live-website-now.md), [Azure-övervakning för app Services](app/azure-web-apps.md), [Azure-övervakning för virtuell dator eller skalnings uppsättning för virtuella datorer](app/azure-vm-vmss-apps.md), eller [Application Insights insamlad skrivare](app/java-collectd.md).
* [Anpassade händelser och mått](app/api-custom-events-metrics.md) som du kodar.
* [Spårnings loggar](app/asp-net-trace-logs.md) om du konfigurerar lämplig insamlare.

Från [klient webb sidor](app/javascript.md):

* [Antal sid visningar](app/usage-overview.md)
* [AJAX-anrop](app/asp-net-dependencies.md) Begär Anden som görs från ett skript som körs.
* Läsa in data för sid visning
* Antal användare och sessioner
* [Autentiserade användar-ID: n](app/api-custom-events-metrics.md#authenticated-users)

Från andra källor, om du konfigurerar dem:

* [Azure Diagnostics](platform/diagnostics-extension-to-application-insights.md)
* [Importera till analys](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan jag filtrera ut eller ändra vissa telemetri?

Ja, på den server som du kan skriva:

* Telemetri-processor för att filtrera eller lägga till egenskaper för valda telemetridata innan de skickas från din app.
* Telemetri-initierare för att lägga till egenskaper till alla objekt i telemetri.

Läs mer om [ASP.net](app/api-filtering-sampling.md) eller [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hur beräknas stad, land/region och annan Geo-plats data?

Vi letar upp IP-adressen (IPv4 eller IPv6) för webb klienten med hjälp av [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Webb läsar telemetri: vi samlar in avsändarens IP-adress.
* Server telemetri: Application Insights-modulen samlar in klientens IP-adress. Den samlas inte in om `X-Forwarded-For` har angetts.
* Mer information om hur IP-adress och data för geolokalisering samlas in i Application Insights finns i den här [artikeln](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Du kan konfigurera `ClientIpHeaderTelemetryInitializer` att ta IP-adressen från en annan rubrik. I vissa system flyttas den till exempel av en proxy, belastningsutjämnare eller CDN till `X-Originating-IP`. [Läs mer](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Du kan [använda Power BI](app/export-power-bi.md ) för att visa din begär ande telemetri på en karta.


### <a name="data"></a>Hur länge sparas data i portalen? Är det säkert?
Ta en titt på [data kvarhållning och sekretess][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Vad händer med att program insikter telemetri när en server eller enhet förlorar anslutning till Azure?

Alla våra SDK: er, inklusive Web SDK, innehåller "Reliable transport" eller "robust transport". När servern eller enheten förlorar anslutning till Azure [lagras telemetri lokalt i fil systemet](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (Server-SDK: er) eller i HTML5-session lagring (Web SDK). SDK gör ett nytt försök att skicka Telemetrin igen tills vår inmatnings tjänst tar hänsyn till "föråldrad" (48 timmar för loggar, 30 minuter för mått). Inaktuell telemetri kommer att tas bort. I vissa fall, t. ex. när den lokala lagringen är full, görs inget nytt försök.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kan personliga data skickas i telemetri?

Detta är möjligt om din kod skickar sådana data. Det kan också hända om variabler i stack spår innehåller person uppgifter. Utvecklings gruppen bör genomföra riskbedömningar för att säkerställa att person uppgifter hanteras korrekt. [Läs mer om data kvarhållning och sekretess](app/data-retention-privacy.md).

**Alla** oktetter i klient webb adressen är alltid inställda på 0 när attributen för geo-platsen har sökts upp.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Min Instrumentation-nyckel är synlig i min webb Side källa. 

* Det här är en vanlig metod för övervakning av lösningar.
* Det kan inte användas för att stjäla dina data.
* Det kan användas för att skeva data eller utlösa aviseringar.
* Vi har inte hört att någon kund har haft sådana problem.

Du kan:

* Använd två separata instrument knappar (separata Application Insights-resurser) för klient-och Server data. Eller
* Skriv en proxy som körs på servern och låt webb klienten skicka data via den proxyservern.

### <a name="post"></a>Hur gör jag för att se skicka data i diagnostisk sökning?
Vi loggar inte in POST-data automatiskt, men du kan använda ett TrackTrace-anrop: Lägg till data i parametern Message. Det finns en längre storleks gräns än gränserna för sträng egenskaper, men det går inte att filtrera på den.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Bör jag använda en eller flera Application Insights resurser?

Använd en enda resurs för alla komponenter eller roller i ett enda företags system. Använd separata resurser för utveckling, test och versions versioner och för oberoende program.

* [Se diskussionen här](app/separate-resources.md)
* [Exempel – moln tjänst med Worker-och Web-roller](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hur gör jag för att du ändra Instrumentation-nyckeln dynamiskt?

* [Diskussion här](app/separate-resources.md)
* [Exempel – moln tjänst med Worker-och Web-roller](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Vad är antalet användare och sessioner?

* Java Script SDK anger en användar-cookie på webb klienten, för att identifiera användare och en sessions-cookie för att gruppera aktiviteter.
* Om det inte finns något skript på klient sidan kan du [Ange cookies på servern](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Om en riktig användare använder din webbplats i olika webbläsare, eller använder privat/Incognito-surfning eller olika datorer, kommer de att räknas mer än en gång.
* Om du vill identifiera en inloggad användare på datorer och webbläsare lägger du till ett anrop till [setAuthenticatedUserContext ()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="q17"></a>Har jag aktiverat allt i Application Insights?
| Vad du bör se | Så här hämtar du det | Varför du vill ha den |
| --- | --- | --- |
| Tillgänglighets diagram |[Webbtester](app/monitor-web-app-availability.md) |Vet att din webbapp är igång |
| Server App-prestanda: svars tider,... |[Lägg till Application Insights i projektet](app/asp-net.md) eller [installera AI-statusövervakare på servern](app/monitor-performance-live-website-now.md) (eller Skriv din egen kod för att [spåra beroenden](app/api-custom-events-metrics.md#trackdependency)) |Identifiera prestanda problem |
| Beroende telemetri |[Installera AI-Statusövervakare på servern](app/monitor-performance-live-website-now.md) |Diagnostisera problem med databaser eller andra externa komponenter |
| Hämta stack spår från undantag |[Infoga TrackException-anrop i koden](app/asp-net-exceptions.md) (men vissa rapporteras automatiskt) |Identifiera och diagnostisera undantag |
| Sök logg spårningar |[Lägg till ett loggnings kort](app/asp-net-trace-logs.md) |Diagnostisera undantag, prestanda problem |
| Grundläggande om klient användning: sidvyer, sessioner,... |[JavaScript-initierare på webb sidor](app/javascript.md) |Användningsanalys |
| Anpassade mått för klienter |[Spåra samtal på webb sidor](app/api-custom-events-metrics.md) |Förbättra användar upplevelsen |
| Anpassade mått för Server |[Spåra anrop i Server](app/api-custom-events-metrics.md) |Business Intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Varför är antalet i Sök-och mått diagram desamma?

[Sampling](app/sampling.md) minskar antalet telemetridata (begär Anden, anpassade händelser osv.) som faktiskt skickas från din app till portalen. I sökningen visas antalet objekt som faktiskt har tagits emot. I mått diagram som visar antalet händelser visas antalet ursprungliga händelser som har inträffat. 

Varje objekt som överförs har en `itemCount`-egenskap som visar hur många ursprungliga händelser som objektet representerar. Du kan köra den här frågan i Analytics för att se hur prover fungerar:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Konfigurera Application Insights

Du kan [skriva PowerShell-skript](app/powershell.md) med hjälp av Azure resursövervakare för att:

* Skapa och uppdatera Application Insights-resurser.
* Ange pris planen.
* Hämta Instrumentation-nyckeln.
* Lägg till en måtta varning.
* Lägg till ett tillgänglighets test.

Du kan inte konfigurera en Metric Explorer-rapport eller konfigurera löpande export.

#### <a name="querying-the-telemetry"></a>Fråga telemetri

Använd [REST API](https://dev.applicationinsights.io/) för att köra [analys](app/analytics.md) frågor.

### <a name="how-can-i-set-an-alert-on-an-event"></a>Hur kan jag ange en avisering för en händelse?

Azure-aviseringar är bara på mått. Skapa ett anpassat mått som korsar ett värde tröskelvärde när händelsen inträffar. Ange sedan en avisering för måttet. Du får ett meddelande när måttet korsar tröskelvärdet i båda riktningarna. du får inget meddelande förrän den första korsningen, oavsett om det ursprungliga värdet är högt eller lågt. Det finns alltid en fördröjning på några minuter.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Kostar data överföring mellan en Azure-webbapp och Application Insights?

* Om din Azure-webbapp finns i ett Data Center där det finns en Application Insights samlings slut punkt finns det ingen kostnad. 
* Om det inte finns någon samlings slut punkt i värd data centret kommer din apps telemetri att debiteras [utgående Azure-avgifter](https://azure.microsoft.com/pricing/details/bandwidth/).

Detta är inte beroende av var Application Insights resursen finns. Det beror bara på distributionen av våra slut punkter.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan jag skicka telemetri till Application Insights-portalen?

Vi rekommenderar att du använder våra SDK [: er](app/api-custom-events-metrics.md)och använder SDK-API: et. Det finns varianter av SDK för olika [plattformar](app/platforms.md). Dessa SDK: er hanterar buffring, komprimering, begränsning, nya försök och så vidare. Men inmatnings [schema](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) och [slut punkts protokoll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) är offentliga.

### <a name="can-i-monitor-an-intranet-web-server"></a>Kan jag övervaka en intranät webb server?

Ja, men du måste tillåta trafik till våra tjänster genom brand Väggs undantag eller omdirigering av proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Läs vår fullständiga lista över tjänster och IP-adresser [här](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Brand Väggs undantag

Tillåt att din webb server skickar telemetri till våra slut punkter. 

#### <a name="gateway-redirect"></a>Omdirigera Gateway

Dirigera trafik från servern till en gateway på intranätet genom att skriva över slut punkter i konfigurationen. Om dessa "slut punkts egenskaper" inte finns i konfigurationen, använder dessa klasser standardvärdena som visas nedan i exemplet ApplicationInsights. config. 

Din gateway ska dirigera trafik till vår slut punkts bas adress. I konfigurationen ersätter du standardvärdena med `http://<your.gateway.address>/<relative path>`.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exempel på ApplicationInsights. config med standard slut punkter:
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

> [!NOTE]
> ApplicationIdProvider är tillgängligt från och med v 2.6.0.



#### <a name="proxy-passthrough"></a>Proxy-genomströmning

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
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan jag köra tillgänglighets webb test på en intranät Server?

Våra [webbtester](app/monitor-web-app-availability.md) körs på närvaro punkter som distribueras världen över. Det finns två lösningar:

* Brand Väggs dörr – Tillåt förfrågningar till servern från [den långa och den ändrings bara listan med webb test agenter](app/ip-addresses.md).
* Skriv din egen kod för att skicka periodiska förfrågningar till servern inifrån intranätet. Du kan köra Visual Studio-webbtester för detta ändamål. Testaren kan skicka resultatet till Application Insights med hjälp av TrackAvailability ()-API: et.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Hur lång tid tar det för telemetri att samlas in?

De flesta Application Insights data har en fördröjning på under 5 minuter. Vissa data kan ta längre tid. oftast större loggfiler. Mer information finns i [service avtalet för Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md



## <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Monitor för behållare. Om du har ytterligare frågor om lösningen går du till [diskussions forumet](https://feedback.azure.com/forums/34192--general-feedback) och publicerar dina frågor. När en fråga är vanliga vi lägga till det i den här artikeln så att den finns snabbt och enkelt.

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Jag ser inte bild-och namn egenskaps värden ifyllda när jag frågar ContainerLog-tabellen.

För agent version ciprod12042019 och senare fylls dessa två egenskaper i som standard för varje loggnings rad för att minimera kostnaderna för loggdata som samlas in. Det finns två alternativ för att fråga tabellen som innehåller dessa egenskaper med sina värden:

#### <a name="option-1"></a>alternativ 1 

Gå med i andra tabeller för att inkludera dessa egenskaps värden i resultaten.

Ändra dina frågor för att inkludera bild-och ImageTag-egenskaper från tabellen ```ContainerInventory``` genom att koppla till egenskapen ContainerID. Du kan inkludera egenskapen namn (som tidigare fanns i ```ContainerLog``` tabellen) från KubepodInventory-tabellens ContaineName-fält genom att koppla till egenskapen ContainerID. Detta är det rekommenderade alternativet.

Följande exempel är ett exempel på en detaljerad fråga som förklarar hur du hämtar dessa fält värden med kopplingar.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Alternativ 2

Återaktivera insamling för de här egenskaperna för varje behållar logg rad.

Om det första alternativet inte är bekvämt på grund av frågor som har inaktiverats kan du återaktivera insamling av de här fälten genom att aktivera inställningen ```log_collection_settings.enrich_container_logs``` i agentens konfigurations mappning enligt beskrivningen i [konfigurations inställningarna för data insamling](insights/container-insights-agent-config.md).

> [!NOTE]
> Det andra alternativet rekommenderas inte med stora kluster som har fler än 50 noder eftersom det genererar API-Server anrop från varje nod i klustret för att utföra denna anrikning. Det här alternativet ökar också data storleken för varje logg rad som samlas in.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Kan jag visa mått som samlats in i Grafana?

Azure Monitor for containers stöder visning av mått som lagras i din Log Analytics arbets yta i Grafana-instrumentpaneler. Vi har angett en mall som du kan ladda ned från Grafana för [instrument panelen](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) för att komma igång och referera till att hjälpa dig att fråga efter ytterligare data från de övervakade klustren för att visualisera anpassade Grafana-instrumentpaneler. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Kan jag övervaka mitt AKS-kluster med Azure Monitor för behållare?

Azure Monitor för behållare stöder övervakning av arbets belastningar som distribueras till AKS-motorn (tidigare ACS-motor) som finns på Azure. Mer information och en översikt över de steg som krävs för att aktivera övervakning för det här scenariot finns i [använda Azure Monitor för behållare för AKS-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Varför visas inte data i min Log Analytics-arbetsyta?

Om du inte kan se några data i Log Analytics arbets ytan vid en viss tid varje dag, kan du ha uppnått standard gränsen på 500 MB eller den dagliga begränsningen för att styra mängden data som ska samlas in dagligen. När gränsen uppfylls för dagen stoppas data insamlingen och återupptas bara på nästa dag. Information om hur du granskar din data användning och uppdaterar till en annan pris nivå baserat på dina förväntade användnings mönster finns i [logga data användning och kostnad](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Vilka är behållar tillstånden angivna i ContainerInventory-tabellen?

Tabellen ContainerInventory innehåller information om både stoppade och pågående behållare. Tabellen fylls i av ett arbets flöde i agenten som frågar Docker efter alla behållare (körs och stoppas) och vidarebefordrar dessa data till Log Analytics arbets ytan.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hur gör jag för att lösa *saknade prenumerations registrerings* fel?

Om du får ett fel meddelande om att **prenumerations registrering saknas för Microsoft. OperationsManagement**kan du lösa det genom att registrera resurs leverantören **Microsoft. OperationsManagement** i prenumerationen där arbets ytan definieras. Dokumentationen för hur du kan göra detta finns [här](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Finns det stöd för RBAC-aktiverade AKS-kluster?

Lösningen för övervakning av behållare stöder inte RBAC, men den stöds med Azure Monitor för behållare. Sidan lösnings information visar kanske inte rätt information i bladet som visar data för de här klustren.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hur gör jag för att aktivera logg insamling för behållare i namn området Kube-system via Helm?

Logg samlingen från behållare i Kube-systemets namnrymd är inaktive rad som standard. Logg insamling kan aktive ras genom att ställa in en miljö variabel på omsagent. Mer information finns på sidan [Azure Monitor for containers](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hur gör jag för att du uppdatera omsagent till den senaste versionen?

Information om hur du uppgraderar agenten finns i [agent hantering](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Hur gör jag för att aktivera loggning på flera rader?

För närvarande har Azure Monitor för behållare inte stöd för flera rader, men det finns lösningar som är tillgängliga. Du kan konfigurera alla tjänster som ska skrivas i JSON-format och sedan Docker/Moby att skriva dem som en enda rad.

Du kan till exempel figursätta loggen som ett JSON-objekt, som du ser i exemplet nedan för ett exempel på Node. js-program:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Dessa data kommer att se ut som i följande exempel i Azure Monitor för loggar när du frågar efter den:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

En detaljerad översikt över problemet finns i följande [GitHub-länk](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hur gör jag för att lösa Azure AD-fel när jag aktiverar Live-loggar? 

Följande fel kan visas: svars-URL: en som **anges i begäran matchar inte de svars-URL: er som har kon figurer ATS för programmet: < program-ID\>** . Lösningen för att lösa problemet finns i artikeln [så här visar du behållar data i real tid med Azure Monitor för behållare](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Varför kan jag inte uppgradera klustret efter onboarding?

Om du när du har aktiverat Azure Monitor för behållare för ett AKS-kluster tar du bort arbets ytan Log Analytics som klustret skickade data till, vid försök att uppgradera klustret. För att undvika detta måste du inaktivera övervakning och sedan återaktivera den till en annan giltig arbets yta i din prenumeration. När du försöker utföra kluster uppgraderingen igen bör den bearbetas och slutföras.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Vilka portar och domäner behöver jag öppna/vitlista för agenten?

Se [nätverks brand Väggs kraven](insights/container-insights-onboard.md#network-firewall-requirements) för proxy-och brand Väggs konfigurations information som krävs för behållarens agent med Azure, Azure amerikanska myndigheter och Azure Kina 21Vianet-moln.

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (för hands version)
Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Monitor for VMs. Om du har ytterligare frågor om lösningen går du till [diskussions forumet](https://feedback.azure.com/forums/34192--general-feedback) och publicerar dina frågor. När en fråga är vanliga vi lägga till det i den här artikeln så att den finns snabbt och enkelt.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Kan jag publicera till en befintlig arbets yta?
Om de virtuella datorerna redan är anslutna till en Log Analytics arbets yta kan du fortsätta att använda arbets ytan vid inregistrering till Azure Monitor for VMs, förutsatt att den är i någon av de regioner som [stöds.](insights/vminsights-enable-overview.md#prerequisites)

Vid onboarding konfigurerar vi prestanda räknare för arbets ytan som gör att alla de virtuella datorerna rapporterar data till arbets ytan för att börja samla in den här informationen för visning och analys i Azure Monitor for VMs.  Därför visas prestanda data från alla virtuella datorer som är anslutna till den valda arbets ytan.  Funktionerna för hälso tillstånd och kartor är bara aktiverade för de virtuella datorer som du har angett att publicera.

Mer information om vilka prestanda räknare som aktive ras finns i artikeln [Aktivera översikt](insights/vminsights-enable-overview.md#performance-counters-enabled) .

### <a name="can-i-onboard-to-a-new-workspace"></a>Kan jag publicera till en ny arbets yta? 
Om dina virtuella datorer inte är anslutna till en befintlig Log Analytics arbets yta måste du skapa en ny arbets yta för att lagra dina data. Att skapa en ny standard arbets yta görs automatiskt om du konfigurerar en enskild virtuell Azure-dator för Azure Monitor for VMs via Azure Portal.

Om du väljer att använda den skriptbaserade metoden beskrivs de här stegen i artikeln [aktivera Azure Monitor for VMS (för hands version) med hjälp av Azure PowerShell eller Resource Manager-mall](insights/vminsights-enable-at-scale-powershell.md) . 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Vad gör jag om min virtuella dator redan rapporterar till en befintlig arbets yta?
Om du redan samlar in data från dina virtuella datorer kanske du redan har konfigurerat den för att rapportera data till en befintlig Log Analytics-arbetsyta.  Så länge arbets ytan finns i en av våra regioner som stöds, kan du aktivera Azure Monitor for VMs till den befintliga arbets ytan.  Om den arbets yta som du redan använder inte finns i någon av våra regioner som stöds, kan du inte publicera till Azure Monitor for VMs för tillfället.  Vi arbetar aktivt för att stödja fler regioner.

>[!NOTE]
>Vi konfigurerar prestanda räknare för arbets ytan som påverkar alla virtuella datorer som rapporterar till arbets ytan, oavsett om du har valt att publicera dem på Azure Monitor for VMs. Mer information om hur prestanda räknare konfigureras för arbets ytan finns i vår [dokumentation](platform/data-sources-performance-counters.md). Information om de räknare som har kon figurer ATS för Azure Monitor for VMs finns i artikeln [aktivera Azure Monitor for VMS](insights/vminsights-enable-overview.md#performance-counters-enabled) .  

### <a name="why-did-my-vm-fail-to-onboard"></a>Varför gick det inte att publicera den virtuella datorn?
När du registrerar en virtuell Azure-dator från Azure Portal inträffar följande steg:

* Standard Log Analytics-arbetsytan skapas, om som har valts.
* Prestanda räknarna konfigureras för den valda arbets ytan. Om det här steget Miss lyckas ser du att några av prestanda diagrammen och tabeller inte visar data för den virtuella datorn som du har registrerat. Du kan åtgärda detta genom att köra PowerShell-skriptet som dokumenteras [här](insights/vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* Log Analytics Agent installeras på virtuella Azure-datorer med ett VM-tillägg, om det är klart krävs det.  
* Azure Monitor for VMs mappnings beroende Agent installeras på virtuella Azure-datorer med ett tillägg, om det är bestämt krävs det.  
* Azure Monitor-komponenter som stöder hälsofunktionen konfigureras vid behov, och den virtuella datorn är konfigurerad för att rapportera hälso data.

Under onboard-processen kontrollerar vi status för var och en av ovanstående för att returnera en meddelande status till dig i portalen. Konfiguration av arbetsytan och agentinstallationen brukar ta 5 – 10 minuter. Det tar ytterligare 5 till 10 minuter att Visa övervaknings-och hälso data i portalen.  

Om du har initierat onboarding och ser meddelanden som anger att den virtuella datorn måste registreras, kan du göra upp till 30 minuter för att den virtuella datorn ska kunna slutföra processen. 

### <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Jag har bara aktiverat Azure Monitor for VMs, varför ser jag alla mina virtuella datorer som övervakas av hälso funktionen?
Hälso funktionen är aktive rad för alla virtuella datorer som är anslutna till Log Analytics arbets ytan, även när åtgärden initieras för en enskild virtuell dator.

### <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Kan jag ändra schemat för när hälso villkoren utvärderar ett villkor?
Nej, tids perioden och frekvensen för hälso villkor kan inte ändras i den här versionen. 

### <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Kan jag inaktivera hälso kriterier för ett villkor som jag inte behöver övervaka?
Hälso kriterier kan inte inaktive ras i den här versionen.

### <a name="are-the-health-alert-severities-configurable"></a>Kan hälso varnings allvarlighets grader konfigureras?  
Det går inte att ändra allvarlighets grad för hälso avisering, de kan bara aktive ras eller inaktive ras. Dessutom kan vissa varnings allvarlighets grader uppdateras baserat på hälso tillståndet. 

### <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Om jag konfigurerar om inställningarna för ett visst hälso villkor, kan de begränsas till en viss instans?  
Om du ändrar någon inställning för en hälso tillstånds instans ändras alla hälso villkors instanser av samma typ på den virtuella Azure-datorn. Till exempel om tröskelvärdet på disk ledigt utrymme hälsotillstånd kriterium instansen för logisk disk C: ändras, gäller detta tröskelvärde alla logiska diskar som identifieras och övervakas för samma virtuella dator.

### <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Övervakar hälso funktionen logiska processorer och kärnor?
Nej, enskilda processor-och logiska processor nivå hälso kriterier ingår inte i ett Windows, endast den totala processor användningen övervakas som standard för att effektivt utvärdera processor belastningen baserat på det totala antalet logiska processorer som är tillgängliga för den virtuella Azure-datorn. 

### <a name="are-all-health-criteria-thresholds-configurable"></a>Kan alla tröskelvärden för hälso kriterier konfigureras?  
Tröskelvärden för hälso villkor som riktar sig mot en virtuell Windows-dator kan inte ändras eftersom deras hälso tillstånd är inställda på att *köras* eller *är tillgängliga*. När du frågar hälso tillståndet från API: [t för arbets belastnings övervakning](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)visas *comparisonOperator* -värdet för **LessThan** eller **GreaterThan** med *tröskelvärdet* **4** för tjänsten eller entiteten om:
   - Tjänstehälsa för DNS-klienten – tjänst inte körs. 
   - Tjänstehälsa för DHCP-klient – tjänst inte körs. 
   - Tjänstehälsa för RPC-tjänsten inte körs. 
   - Tjänstehälsa för Windows-brandväggen – tjänst inte körs.
   - Tjänstehälsa för Windows-händelseloggen – tjänst inte körs. 
   - Tjänstehälsa för Server – tjänst inte körs. 
   - Windows remote management service health – tjänsten körs inte. 
   - Filsystemfel eller skadade data – logisk Disk är inte tillgänglig.

Tröskelvärden för följande Linux-hälsovillkor kan inte ändras eftersom deras hälso tillstånd redan har angetts till *True*. Hälso tillståndet visar *comparisonOperator* med värdet **LessThan** och *tröskelvärdet* **1** när det efter frågas från API: et för arbets belastnings övervakning för entiteten, beroende på dess kontext:
   - Diskstatus för logisk – logisk disk är inte online / tillgängliga
   - Diskstatus – Disk är inte online / tillgängliga
   - Nätverkskortets Status - nätverkskort är inaktiverad

### <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Hur gör jag för att ändra aviseringar som ingår i hälso funktionen?
Varningsregler som har definierats för varje hälsotillstånd kriterium visas inte i Azure-portalen. Du kan endast aktivera eller inaktivera en hälso aviserings regel i [API: t för arbets belastnings övervakning](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Du kan inte heller tilldela en [Azure Monitor åtgärds grupp](platform/action-groups.md) för hälso aviseringar i Azure Portal. Du kan bara använda API: et för aviserings inställningar för att konfigurera en åtgärds grupp som ska utlösas när en hälso varning utlöses. För närvarande kan du tilldela åtgärds grupper mot en virtuell dator så att alla *hälso aviseringar* som utlöses mot den virtuella datorn utlöser samma åtgärds grupper. Till skillnad från traditionella Azure-aviseringar finns det ingen i en separat åtgärdsgrupp för varje health-aviseringsregel. Dessutom stöds endast åtgärdsgrupper som är konfigurerade för att tillhandahålla e-post eller SMS-meddelanden när hälsovarningar utlöses. 

### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Jag ser inte vissa eller alla data i prestandadiagram för den virtuella datorn
Våra prestanda diagram har uppdaterats för att använda data som lagras i *InsightsMetrics* -tabellen.  Om du vill se data i dessa diagram måste du uppgradera för att använda den nya lösningen för VM-insikter.  Mer information finns i [vanliga frågor och svar om ga](insights/vminsights-ga-release-faq.md) .

Om du inte ser prestanda data i disk tabellen eller i vissa prestanda diagram, kanske prestanda räknarna inte konfigureras på arbets ytan. Kör följande [PowerShell-skript](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell)för att lösa problemet.


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Hur skiljer sig Azure Monitor for VMs Map-funktionen från Tjänstkarta?
Azure Monitor for VMs Map-funktionen baseras på Tjänstkarta, men har följande skillnader:

* Du kan komma åt kart visningen från bladet VM och från Azure Monitor for VMs under Azure Monitor.
* Anslutningarna på kartan är nu klickbara och visar en vy över anslutnings måttets data på sido panelen för den valda anslutningen.
* Det finns ett nytt API som används för att skapa Maps för att bättre stödja mer komplexa kartor.
* Övervakade virtuella datorer ingår nu i noden klient grupp och ring diagrammet visar förhållandet mellan övervakade och oövervakade virtuella datorer i gruppen.  Den kan också användas för att filtrera listan över datorer när gruppen expanderas.
* Övervakade virtuella datorer ingår nu i noderna i server port gruppen och ring diagrammet visar förhållandet mellan övervakade och oövervakade datorer i gruppen.  Den kan också användas för att filtrera listan över datorer när gruppen expanderas.
* Kart formatet har uppdaterats så att det blir mer konsekvent med app Map från Application Insights.
* Sido panelerna har uppdaterats och har inte den fullständiga uppsättningen integreringar som stöds i Tjänstkarta-Uppdateringshantering, Ändringsspårning, säkerhet och Service Desk. 
* Alternativet för att välja grupper och datorer som ska mappas har uppdaterats och stöder nu prenumerationer, resurs grupper, skalnings uppsättningar för virtuella Azure-datorer och moln tjänster.
* Du kan inte skapa nya Tjänstkarta dator grupper i Azure Monitor for VMs Map-funktionen.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Varför visar min prestandadiagram kantlinjerna?
Detta kan inträffa av några skäl.  I de fall där det finns en lucka i data insamlingen visas linjerna som prickade.  Om du har ändrat data samplings frekvensen för aktiverade prestanda räknare (Standardinställningen är att samla in data var 60 sekund), kan du se prickade linjer i diagrammet om du väljer ett smalt tidsintervall för diagrammet och samplings frekvensen är mindre än den Bucket-storlek som används i diagrammet (till exempel samplings frekvensen är var tionde minut och varje Bucket i diagrammet är 5 minuter).  Om du väljer ett bredare tidsintervall som ska visas bör det leda till att diagram linjerna visas som heldragna linjer i stället för punkter i det här fallet.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Grupper stöds med Azure Monitor för virtuella datorer?
Ja, när du har installerat beroende agenten samlar vi in information från de virtuella datorerna för att Visa grupper baserat på prenumeration, resurs grupp, skalnings uppsättningar för virtuella datorer och moln tjänster.  Om du har använt Tjänstkarta och skapat dator grupper visas dessa även.  Dator grupper visas också i filtret grupper om du har skapat dem för den arbets yta som du visar. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hur gör jag för att se information om vad som driver den 95 percentils linjen i de sammanställda prestanda diagrammen?
Som standard sorteras listan för att visa de virtuella datorer som har det högsta värdet för 95-percentilen för det valda måttet, förutom det tillgängliga minnes diagrammet, som visar datorerna med det lägsta värdet för den femte percentilen.  Om du klickar på diagrammet öppnas den **översta N-listvyn** med lämpligt mått valt.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hur hanterar kart funktionen dubbla IP-adresser över olika virtuella nätverk och undernät?
Om du duplicerar IP-intervall med virtuella datorer eller skalnings uppsättningar för virtuella Azure-datorer över undernät och virtuella nätverk kan det orsaka att Azure Monitor for VMs karta visar felaktig information. Detta är ett känt problem och vi undersöker alternativ för att förbättra den här upplevelsen.

### <a name="does-map-feature-support-ipv6"></a>Stöder kart funktionen IPv6?
Kart funktionen stöder för närvarande endast IPv4 och vi undersöker stödet för IPv6. Vi stöder också IPv4 som är tunnlad i IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>När jag läser in en karta för en resurs grupp eller en annan stor grupp är det svårt att visa kartan
Vi har gjort förbättringar i kartan för att hantera stora och komplexa konfigurationer, men vi inser att en karta kan ha många noder, anslutningar och noder som fungerar som ett kluster.  Vi är måna om du fortsätter att förbättra stöd för att öka skalbarheten.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Varför ser nätverks diagrammet på fliken prestanda annorlunda ut än nätverks diagrammet på översikts sidan för Azure VM?

Översikts sidan för en virtuell Azure-dator visar diagram baserat på värdens mätning av aktivitet på den virtuella gäst datorn.  För nätverks diagrammet på Azure VM-översikten visar det bara nätverks trafik som kommer att faktureras.  Detta omfattar inte trafik mellan virtuella nätverk.  Data och diagram som visas för Azure Monitor for VMs baseras på data från den virtuella gäst datorn och nätverks diagrammet visar all TCP/IP-trafik som är inkommande och utgående till den virtuella datorn, inklusive mellan virtuella nätverk.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hur mäts svars tiden för data som lagras i VMConnection och visas i anslutnings panelen och arbets böcker?

Svars tiden är en uppskattning. Eftersom vi inte instrumenterar koden för programmet, vet vi egentligen inte när en begäran börjar och när svaret kommer. I stället noterar vi data som skickas på en anslutning och sedan data som kommer tillbaka på den anslutningen. Vår agent håller koll på dessa skickar och tar emot och försöker para ihop dem: en sekvens med sändningar, följt av en sekvens med mottagna, tolkas som ett begär ande/svar-par. Tiden mellan dessa åtgärder är svars tiden. Den omfattar nätverks fördröjningen och Server bearbetnings tiden.

Den här uppskattningen fungerar bra för protokoll som är Request/Response-baserade: en enskild begäran skickas över anslutningen och ett enda svar tas emot. Detta är fallet för HTTP (S) (utan pipelinering), men inte uppfyllt för andra protokoll.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Är deras begränsningar om jag är på Log Analytics kostnads fri pris plan?
Om du har konfigurerat Azure Monitor med en Log Analytics arbets yta som använder den *kostnads fria* pris nivån, kommer Azure Monitor for VMS Map-funktionen bara att ha stöd för fem anslutna datorer som är anslutna till arbets ytan. Om du har fem virtuella datorer som är anslutna till en kostnads fri arbets yta, kopplar du från en av de virtuella datorerna och senare ansluter en ny virtuell dator. den nya virtuella datorn övervakas inte och visas inte på kart sidan.  

Under det här tillståndet uppmanas du att välja alternativet **prova nu** när du öppnar den virtuella datorn och väljer **insikter (för hands version)** i det vänstra fönstret, även efter att det redan har installerats på den virtuella datorn.  Du uppmanas dock inte att ange alternativ som normalt skulle inträffa om den virtuella datorn inte har publicerats till Azure Monitor for VMs. 


## <a name="next-steps"></a>Nästa steg
Om din fråga inte besvaras här kan du referera till följande forum för ytterligare frågor och svar.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Om du vill ha allmän feedback på Azure Monitor besöker du [feedback-forumet](https://feedback.azure.com/forums/34192--general-feedback).
