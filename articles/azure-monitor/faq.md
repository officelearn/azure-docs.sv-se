---
title: Vanliga frågor och svar om Azure Monitor | Microsoft-dokument
description: Svar på vanliga frågor om Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2020
ms.openlocfilehash: 777e4e1f8fdd05345d949fe8c78b4a5b1953b8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298250"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Vanliga frågor och svar i Azure Monitor

Den här Vanliga frågor och svar från Microsoft är en lista över vanliga frågor om Azure Monitor.

## <a name="general"></a>Allmänt

### <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
[Azure Monitor](overview.md) är en tjänst i Azure som tillhandahåller prestanda- och tillgänglighetsövervakning för program och tjänster i Azure, andra molnmiljöer eller lokala. Azure Monitor samlar in data från flera källor till en gemensam dataplattform där den kan analyseras för trender och avvikelser. Omfattande funktioner i Azure Monitor hjälper dig att snabbt identifiera och svara på kritiska situationer som kan påverka ditt program.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Vad är skillnaden mellan Azure Monitor, Log Analytics och Application Insights?
I september 2018 kombinerade Microsoft Azure Monitor, Log Analytics och Application Insights i en enda tjänst för att ge kraftfull heltäckande övervakning av dina program och de komponenter de förlitar sig på. Funktionerna i Logganalys och Application Insights har inte ändrats, även om vissa funktioner har bytt namn till Azure Monitor för att bättre återspegla deras nya omfattning. Loggdatamotorn och frågespråket i Log Analytics kallas nu Azure Monitor Logs. Se [Azure Monitor-terminologiuppdateringar](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Vad kostar Azure Monitor?
Funktioner för Azure Monitor som aktiveras automatiskt, till exempel insamling av mått och aktivitetsloggar tillhandahålls utan kostnad. Det finns en kostnad som är associerad med andra funktioner, till exempel loggfrågor och aviseringar. På [prissidan](https://azure.microsoft.com/pricing/details/monitor/) för Azure Monitor finns detaljerad prisinformation på azure monitor.Se azure monitor pricing page for detailed pricing information.

### <a name="how-do-i-enable-azure-monitor"></a>Hur aktiverar jag Azure Monitor?
Azure Monitor aktiveras så fort du skapar en ny Azure-prenumeration och [aktivitetslogg](platform/activity-logs-overview.md) och [plattformsmått](platform/data-platform-metrics.md) samlas in automatiskt. Skapa [diagnostikinställningar](platform/diagnostic-settings.md) för att samla in mer detaljerad information om hur du använder dina Azure-resurser och lägga till [övervakningslösningar](insights/solutions.md) och [insikter](insights/insights-overview.md) för att tillhandahålla ytterligare analyser av insamlade data för vissa tjänster. 

### <a name="how-do-i-access-azure-monitor"></a>Hur kommer jag åt Azure Monitor?
Få tillgång till alla Azure Monitor-funktioner och data från **Monitor-menyn** i Azure-portalen. Avsnittet **Övervakning** på menyn för olika Azure-tjänster ger åtkomst till samma verktyg med data filtrerade till en viss resurs. Azure Monitor-data är också tillgängliga för en mängd olika scenarier med CLI, PowerShell och ett REST API.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Finns det en lokal version av Azure Monitor?
Nej. Azure Monitor är en skalbar molntjänst som bearbetar och lagrar stora mängder data, även om Azure Monitor kan övervaka resurser som är lokala och i andra moln.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Kan Azure Monitor övervaka lokala resurser?
Förutom att samla in övervakningsdata från Azure-resurser kan Azure Monitor samla in data från virtuella datorer och program i andra moln och lokalt. Se [Källor för övervakningsdata för Azure Monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Integreras Azure Monitor med System Center Operations Manager?
Du kan ansluta din befintliga System Center Operations Manager-hanteringsgrupp till Azure Monitor för att samla in data från agenter till Azure Monitor Logs. På så sätt kan du använda loggfrågor och lösning för att analysera data som samlats in från agenter. Du kan också konfigurera befintliga System Center Operations Manager-agenter för att skicka data direkt till Azure Monitor. Se [Anslut Operations Manager till Azure Monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Vilka IP-adresser använder Azure Monitor?
Se [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) för en lista över IP-adresser och portar som krävs för agenter och andra externa resurser för att komma åt Azure Monitor. 

## <a name="monitoring-data"></a>Övervaka data

### <a name="where-does-azure-monitor-get-its-data"></a>Var får Azure Monitor sina data?
Azure Monitor samlar in data från en mängd olika källor, inklusive loggar och mått från Azure-plattform och resurser, anpassade program och agenter som körs på virtuella datorer. Andra tjänster som Azure Security Center och Network Watcher samlar in data på en Log Analytics-arbetsyta så att den kan analyseras med Azure Monitor-data. Du kan också skicka anpassade data till Azure Monitor med REST API för loggar eller mått. Se [Källor för övervakningsdata för Azure Monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Vilka data samlas in av Azure Monitor? 
Azure Monitor samlar in data från en mängd olika källor i [loggar](platform/data-platform-logs.md) eller [mått](platform/data-platform-metrics.md). Varje typ av data har sina egna relativa fördelar och var och en stöder en viss uppsättning funktioner i Azure Monitor. Det finns en databas med en enda mått för varje Azure-prenumeration, medan du kan skapa flera Log Analytics-arbetsytor för att samla in loggar beroende på dina behov. Se [Azure Monitor-dataplattform](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Finns det en maximal mängd data som jag kan samla in i Azure Monitor?
Det finns ingen gräns för hur mycket måttdata du kan samla in, men dessa data lagras i högst 93 dagar. Se [Bevarande av mått](platform/data-platform-metrics.md#retention-of-metrics). Det finns ingen gräns för hur mycket loggdata som du kan samla in, men det kan påverkas av den prisnivå du väljer för Log Analytics-arbetsytan. Se [prisinformation](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Hur kommer jag åt data som samlas in av Azure Monitor?
Insikter och lösningar ger en anpassad upplevelse för att arbeta med data som lagras i Azure Monitor. Du kan arbeta direkt med loggdata med hjälp av en loggfråga skriven i Kusto Query Language (KQL). I Azure-portalen kan du skriva och köra frågor och interaktivt analysera data med Hjälp av Log Analytics. Analysera mått i Azure-portalen med Statistikutforskaren. Se [Analysera loggdata i Azure Monitor](log-query/log-query-overview.md) och komma igång med Azure [Metrics Explorer](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Lösningar och insikter

### <a name="what-is-an-insight-in-azure-monitor"></a>Vad är en insikt i Azure Monitor?
Insikter ger en anpassad övervakningsupplevelse för vissa Azure-tjänster. De använder samma mått och loggar som andra funktioner i Azure Monitor men kan samla in ytterligare data och ge en unik upplevelse i Azure-portalen. Se [Insikter i Azure Monitor](insights/insights-overview.md).

Information om hur du visar statistik i Azure-portalen finns i avsnittet **Insikter** på **Monitor-menyn** eller **avsnittet Övervakning** på tjänstens meny.

### <a name="what-is-a-solution-in-azure-monitor"></a>Vad är en lösning i Azure Monitor?
Övervakningslösningar är paketerade uppsättningar av logik för övervakning av ett visst program eller en viss tjänst baserat på Azure Monitor-funktioner. De samlar in loggdata i Azure Monitor och tillhandahåller loggfrågor och vyer för sin analys med hjälp av en gemensam upplevelse i Azure-portalen. Se [Övervakningslösningar i Azure Monitor](insights/solutions.md).

Om du vill visa lösningar i Azure-portalen klickar du på **Mer** i avsnittet **Insikter** på **Monitor-menyn.** Klicka på **Lägg** till om du vill lägga till ytterligare lösningar på arbetsytan.

## <a name="logs"></a>Loggar

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Vad är skillnaden mellan Azure Monitor Logs och Azure Data Explorer?
Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Monitor Logs bygger ovanpå Azure Data Explorer och använder samma Kusto Query Language (KQL) med några mindre skillnader. Se [Språkskillnader för Azure Monitor-loggfrågor](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Hur hämtar jag loggdata?
Alla data hämtas från en Log Analytics-arbetsyta med hjälp av en loggfråga som skrivs med KQL (Kusto Query Language). Du kan skriva dina egna frågor eller använda lösningar och insikter som innehåller loggfrågor för ett visst program eller en viss tjänst. Se [Översikt över loggfrågor i Azure Monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Vad är en Log Analytics-arbetsyta?
Alla loggdata som samlas in av Azure Monitor lagras på en Log Analytics-arbetsyta. En arbetsyta är i huvudsak en behållare där loggdata samlas in från en mängd olika källor. Du kan ha en enda Log Analytics-arbetsyta för alla dina övervakningsdata eller kan ha krav för flera arbetsytor. Se [Utforma distributionen av Dina Azure Monitor Logs](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Kan du flytta en befintlig Log Analytics-arbetsyta till en annan Azure-prenumeration?
Du kan flytta en arbetsyta mellan resursgrupper eller prenumerationer men inte till en annan region. Se [Flytta en log analytics-arbetsyta till en annan prenumeration eller resursgrupp](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Varför kan jag inte se knapparna Query Explorer och Save i Logganalys?

**Frågeutforskaren,** **Spara** och **Nya varningsregelknappar** är inte tillgängliga när [frågeomfattningen](log-query/scope.md) är inställd på en viss resurs. Om du vill skapa aviseringar, spara eller läsa in en fråga måste Log Analytics begränsas till en arbetsyta. Om du vill öppna Log Analytics i arbetsytans kontext väljer du **Loggar** på **Azure Monitor-menyn.** Den senast använda arbetsytan är markerad, men du kan välja vilken annan arbetsyta som helst. Se [Loggfrågeomfattning och tidsintervall i Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Varför får jag felmeddelandet: "Registrera resursleverantören 'Microsoft.Insights' för den här prenumerationen för att aktivera den här frågan" när jag öppnar Log Analytics från en virtuell dator? 
Många resursleverantörer registreras automatiskt, men du kan behöva registrera vissa resursleverantörer manuellt. Utrymmet för registrering är alltid prenumerationen. Mer information finns i [Resursproviders och resurstyper](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Varför får jag inget felmeddelande om åtkomst när jag öppnar Log Analytics från en virtuell dator? 
Om du vill visa VM-loggar måste du beviljas med läsbehörighet till de arbetsytor som lagrar VM-loggarna. I dessa fall måste administratören ge dig behörigheter i Azure.

## <a name="alerts"></a>Aviseringar

### <a name="what-is-an-alert-in-azure-monitor"></a>Vad är en avisering i Azure Monitor?
Aviseringar meddelar dig proaktivt när viktiga villkor hittas i dina övervakningsdata. De gör att du kan identifiera och åtgärda problem innan användarna av ditt system märker dem. Det finns flera typer av aviseringar:

- Mått - Måttvärdet överskrider ett tröskelvärde.
- Loggfråga - Resultat av en loggfråga matchar definierade villkor.
- Aktivitetslogg - Aktivitetslogghändelse matchar definierade villkor.
- Webbtest - Resultat av tillgänglighetstest matchar definierade kriterier.


Se [Översikt över aviseringar i Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Vad är en aktionsgrupp?
En åtgärdsgrupp är en samling meddelanden och åtgärder som kan utlösas av en avisering. Flera aviseringar kan använda en enda åtgärdsgrupp så att du kan utnyttja vanliga uppsättningar av meddelanden och åtgärder. Se [Skapa och hantera åtgärdsgrupper i Azure-portalen](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Vad är en åtgärdsregel?
Med en åtgärdsregel kan du ändra beteendet för en uppsättning aviseringar som matchar ett visst villkor. På så sätt kan du utföra sådana krav som att inaktivera varningsåtgärder under ett underhållsfönster. Du kan också använda en åtgärdsgrupp på en uppsättning aviseringar i stället för att tillämpa dem direkt på varningsreglerna. Se [Åtgärdsregler](platform/alerts-action-rules.md).

## <a name="agents"></a>Agenter

### <a name="does-azure-monitor-require-an-agent"></a>Kräver Azure Monitor en agent?
En agent krävs endast för att samla in data från operativsystemet och arbetsbelastningar i virtuella datorer. De virtuella datorerna kan finnas i Azure, en annan molnmiljö eller lokalt. Se [Översikt över Azure Monitor-agenter](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Vad är skillnaden mellan Azure Monitor-agenterna?
Azure Diagnostic-tillägget är avsett för virtuella Azure-datorer och samlar in data till Azure Monitor Metrics, Azure Storage och Azure Event Hubs. Log Analytics-agenten är för virtuella datorer i Azure, en annan molnmiljö eller lokalt och samlar in data till Azure Monitor Logs. Beroendeagenten kräver Log Analytics-agenten och samlad processinformation och beroenden. Se [Översikt över Azure Monitor-agenter](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Använder min agenttrafik min ExpressRoute-anslutning?
Trafik till Azure Monitor använder Microsoft peering ExpressRoute-kretsen. Se [ExpressRoute-dokumentation](../expressroute/expressroute-faqs.md#supported-services) för en beskrivning av de olika typerna av ExpressRoute-trafik. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Hur kan jag bekräfta att Log Analytics-agenten kan kommunicera med Azure Monitor?
Välj **Säkerhets- & Inställningar**på Kontrollpanelen på agentdatorn , **Microsoft Monitoring Agent** . Under fliken **Oms (Azure Log Analytics)** bekräftar en grön bockikon att agenten kan kommunicera med Azure Monitor. En gul varningsikon innebär att agenten har problem. En vanlig orsak är **att Tjänsten Microsoft Monitoring Agent** har stoppats. Använd tjänstkontrollhanteraren för att starta om tjänsten.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Hur stoppar jag Log Analytics-agenten från att kommunicera med Azure Monitor?
För agenter som är anslutna till Log Analytics direkt öppnar du Kontrollpanelen och väljer **Inställningar för säkerhet &**, Microsoft Monitoring **Agent**. Ta bort alla arbetsytor som anges under fliken **Azure Log Analytics (OMS).** I System Center Operations Manager tar du bort datorn från listan Logganalyshanterade datorer. Operations Manager uppdaterar konfigurationen av agenten för att inte längre rapportera till Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Hur mycket data skickas per agent?
Mängden data som skickas per agent beror på:

* De lösningar du har aktiverat
* Antalet loggar och prestandaräknare som samlas in
* Mängden data i loggarna

Mer information finns i [Hantera användning och kostnader med Azure Monitor-loggar.](platform/manage-cost-storage.md)

För datorer som kan köra WireData-agenten använder du följande fråga för att se hur mycket data som skickas:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Hur mycket nätverksbandbredd används av Microsoft Management Agent (MMA) när data skickas till Azure Monitor?
Bandbredd är en funktion på mängden data som skickas. Data komprimeras när de skickas över nätverket.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Hur får jag ett meddelande när datainsamlingen från Log Analytics-agenten stoppas?

Följ stegen som beskrivs i [skapa en ny loggavisering som](platform/alerts-metric.md) ska meddelas när datainsamlingen stoppas. Använd följande inställningar för varningsregeln:

- **Definiera varningsvillkor:** Ange arbetsytan Logganalys som resursmål.
- **Varningsvillkor** 
   - **Signalnamn:** *Anpassad loggsökning*
   - **Sökfråga:**`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Varningslogik:** **Baserat på** *antal resultat*, **Villkor** som *är större än*, **Tröskelvärde** *0*
   - **Utvärderad baserat på**: **Period (i minuter)** *30*, **Frekvens (i minuter)** *10*
- **Definiera aviseringsinformationen** 
   - **Namn**: *Datainsamlingen stoppad*
   - **Allvarlighetsgrad**: *Varning*

Ange en befintlig eller ny [åtgärdsgrupp](platform/action-groups.md) så att du meddelas om du har ett pulsslag som saknas i mer än 15 minuter när loggvarningen matchar villkoren.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Vilka är brandväggskraven för Azure Monitor-agenter?
Se [Krav på nätverksbrandvägg](platform/log-analytics-agent.md#network-firewall-requirements)för information om brandväggskrav.


## <a name="visualizations"></a>Visualiseringar

### <a name="why-cant-i-see-view-designer"></a>Varför kan jag inte se Visa designer?

View Designer är endast tillgängligt för användare som tilldelats med deltagarbehörigheter eller högre på log analytics-arbetsytan.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Konfigurationsproblem
*Jag har problem med att konfigurera min:*

* [.NET-app](app/asp-net-troubleshoot-no-data.md)
* [Övervaka en app som redan körs](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure-diagnostik](platform/diagnostics-extension-to-application-insights.md)
* [Java-webbapp](app/java-troubleshoot.md)

*Jag får inga data från min server*

* [Ange brandväggsund undantag](app/ip-addresses.md)
* [Konfigurera en ASP.NET-server](app/monitor-performance-live-website-now.md)
* [Konfigurera en Java-server](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Kan jag använda Application Insights med ...?

* [Webbappar på en IIS-server i Azure VM- eller Azure-skalningsuppsättning för virtuella datorer](app/azure-vm-vmss-apps.md)
* [Webbappar på en IIS-server – lokalt eller i en virtuell dator](app/asp-net.md)
* [Java-webbappar](app/java-get-started.md)
* [Node.js-appar](app/nodejs.md)
* [Webbappar på Azure](app/azure-web-apps.md)
* [Molntjänster på Azure](app/cloudservices.md)
* [Appservrar som körs i Docker](app/docker.md)
* [Ensidiga webbappar](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows-skrivbordsapp](app/windows-desktop.md)
* [Andra plattformar](app/platforms.md)

### <a name="is-it-free"></a>Är det gratis?

Ja, för experimentellt bruk. I den grundläggande prisplanen kan din ansökan skicka en viss mängd data varje månad utan kostnad. Den kostnadsfria ersättningen är tillräckligt stor för att täcka utveckling och publicera en app för ett litet antal användare. Du kan ange ett tak för att förhindra att mer än en viss mängd data bearbetas.

Större volymer telemetri debiteras av Gb. Vi ger några tips om hur du [begränsar dina avgifter.](app/pricing.md)

Enterprise-planen medför en avgift för varje dag som varje webbservernod skickar telemetri. Den är lämplig om du vill använda Kontinuerlig export i stor skala.

[Läs prisplanen](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Vad kostar det?

* Öppna **sidan Användning och uppskattade kostnader** i en Application Insights-resurs. Det finns ett diagram över den senaste användningen. Du kan ange ett datavolymtak om du vill.
* Öppna [Azure Billing-bladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) för att se dina räkningar för alla resurser.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Vad ändrar Application Insights i mitt projekt?
Detaljerna beror på typen av projekt. För ett webbprogram:

* Lägger till dessa filer i projektet:
  * ApplicationInsights.config
  * ai.js (ai.js)
* Installerar dessa NuGet-paket:
  * *API för application insights* - kärn-API:et
  * *Api för application insights för webbprogram* – används för att skicka telemetri från servern
  * *Api för Application Insights för JavaScript-program* – används för att skicka telemetri från klienten
* Paketen innehåller dessa sammansättningar:
  * Microsoft.ApplicationInights Microsoft.ApplicationInsights Microsoft.ApplicationInsights Microsoft.
  * Microsoft.ApplicationInsights.Platform
* Infogar objekt i:
  * Web.config
  * packages.config
* (Endast nya projekt – om du [lägger till Application Insights i ett befintligt projekt][start]måste du göra detta manuellt.) Infogar utdrag i klient- och serverkoden för att initiera dem med application insights-resurs-ID. I en MVC-app infogas till exempel kod i huvudsidan Vyer/Shared/\_Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hur uppgraderar jag från äldre SDK-versioner?
Se [viktig information](app/release-notes.md) för SDK som är lämplig för din typ av program.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Hur ändrar jag vilken Azure-resurs mitt projekt skickar data till?
Högerklicka på `ApplicationInsights.config` i Solution Explorer och välj **Uppdatera programstatistik**. Du kan skicka data till en befintlig eller ny resurs i Azure. Uppdateringsguiden ändrar instrumenteringsnyckeln i ApplicationInsights.config, som avgör var servern SDK skickar dina data. Om du inte avmarkerar "Uppdatera alla" ändras också nyckeln där den visas på webbsidorna.

### <a name="what-is-status-monitor"></a>Vad är Statusövervakaren?

En skrivbordsapp som du kan använda i IIS-webbservern för att konfigurera Application Insights i webbappar. Den samlar inte in telemetri: du kan stoppa den när du inte konfigurerar en app. 

[Läs mer](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Vilken telemetri samlas in av Application Insights?

Från serverwebbappar:

* HTTP-begäranden
* [Beroenden](app/asp-net-dependencies.md). Anrop till: SQL-databaser; HTTP-anrop till externa tjänster. Azure Cosmos DB, tabell, blob-lagring och kö. 
* [Undantag](app/asp-net-exceptions.md) och stackspårningar.
* [Prestandaräknare](app/performance-counters.md) – Om du använder [Statusövervakare](app/monitor-performance-live-website-now.md), [Azure-övervakning för App Services,](app/azure-web-apps.md) [Azure-övervakning för vm eller skalningsuppsättning för virtuella datorer](app/azure-vm-vmss-apps.md)eller [insamlade programinsikter](app/java-collectd.md).
* Anpassade händelser och mått som du [kodar.](app/api-custom-events-metrics.md)
* [Spåra loggar](app/asp-net-trace-logs.md) om du konfigurerar lämplig insamlare.

Från [klientens webbsidor:](app/javascript.md)

* [Antal sidvisningar](app/usage-overview.md)
* [AJAX samtal](app/asp-net-dependencies.md) Begäranden från ett skript som körs.
* Läsa in data för sidvy
* Antal användare och session
* [Autentiserade användar-ID:er](app/api-custom-events-metrics.md#authenticated-users)

Från andra källor, om du konfigurerar dem:

* [Azure-diagnostik](platform/diagnostics-extension-to-application-insights.md)
* [Importera till Analytics](platform/data-collector-api.md)
* [Logga Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan jag filtrera bort eller ändra någon telemetri?

Ja, på servern kan du skriva:

* Telemetriprocessor för att filtrera eller lägga till egenskaper i valda telemetriobjekt innan de skickas från appen.
* Telemetriinitierare om du vill lägga till egenskaper i alla telemetriobjekt.

Läs mer för [ASP.NET](app/api-filtering-sampling.md) eller [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hur beräknas stad, land/region och andra geoplatsdata?

Vi slår upp IP-adressen (IPv4 eller IPv6) för webbklienten med [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Webbläsare telemetri: Vi samlar avsändarens IP-adress.
* Servertelemetri: Modulen Application Insights samlar in klient-IP-adressen. Det samlas inte `X-Forwarded-For` in om den är inställd.
* Mer information om hur IP-adress- och geolokaliseringsdata samlas in i Application Insights finns i den här [artikeln](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Du kan `ClientIpHeaderTelemetryInitializer` konfigurera att ta IP-adressen från ett annat huvud. I vissa system flyttas den till exempel av en proxy, belastningsutjämnare eller CDN till `X-Originating-IP`. [Läs mer](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Du kan [använda Power BI](app/export-power-bi.md ) för att visa din begäran telemetri på en karta.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Hur länge lagras data i portalen? Är den säker?
Ta en titt på [datalagring och sekretess][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Vad händer med Application Insights telemetri när en server eller enhet förlorar anslutningen till Azure?

Alla våra SDK:er, inklusive webben SDK, innehåller "pålitlig transport" eller "robust transport". När servern eller enheten förlorar anslutningen till Azure lagras telemetri [lokalt i filsystemet](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (Server SDK: er) eller i HTML5 Session Storage (Web SDK). SDK kommer regelbundet att försöka skicka denna telemetri tills vår intag tjänst anser att det "inaktuella" (48-timmar för loggar, 30 minuter för mått). Inaktuell telemetri kommer att släppas. I vissa fall, till exempel när det lokala lagringsutrymmet är fullt, kommer inget nytt försök att ske.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kan personuppgifter skickas i telemetrin?

Detta är möjligt om din kod skickar sådana data. Det kan också hända om variabler i stackspårningar innehåller personuppgifter. Ditt utvecklingsteam bör göra riskbedömningar för att säkerställa att personuppgifter hanteras korrekt. [Läs mer om datalagring och sekretess](app/data-retention-privacy.md).

**Alla** oktetter av klientens webbadress är alltid inställda på 0 när geo-platsattributen har tittats upp.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Min instrumenteringsnyckel visas i webbsidans källa. 

* Detta är vanligt i övervakningslösningar.
* Den kan inte användas för att stjäla dina data.
* Den kan användas för att förvränga dina data eller utlösa aviseringar.
* Vi har inte hört att någon kund har haft sådana problem.

Du kan:

* Använd två separata instrumenteringsnycklar (separata Application Insights-resurser) för klient- och serverdata. Eller
* Skriv en proxy som körs på servern och be webbklienten skicka data via den proxyn.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Hur ser jag POST-data i diagnostiksökning?
Vi loggar inte POST-data automatiskt, men du kan använda ett TrackTrace-samtal: placera data i meddelandeparametern. Detta har en längre storleksgräns än gränserna för strängegenskaper, men du kan inte filtrera på den.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Ska jag använda resurser för enkel- eller flera Application Insights?

Använd en enda resurs för alla komponenter eller roller i ett enda affärssystem. Använd separata resurser för utvecklings-, test- och utgivningsversioner och för oberoende program.

* [Se diskussionen här](app/separate-resources.md)
* [Exempel - molntjänst med arbetar- och webbroller](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hur ändrar jag instrumenteringsnyckeln dynamiskt?

* [Diskussion här](app/separate-resources.md)
* [Exempel - molntjänst med arbetar- och webbroller](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Vad räknas antalet användare och session?

* JavaScript SDK ställer in en användarcookie på webbklienten för att identifiera återkommande användare och en sessionscookie till gruppaktiviteter.
* Om det inte finns något klientskript kan du [ställa in cookies på servern](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Om en verklig användare använder din webbplats i olika webbläsare, eller använder privat/inkognitosurfning, eller olika maskiner, räknas de mer än en gång.
* Om du vill identifiera en inloggad användare mellan datorer och webbläsare lägger du till ett anrop för att [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a>Har jag aktiverat allt i Application Insights?
| Vad du bör se | Hur får man det | Varför du vill ha det |
| --- | --- | --- |
| Tillgänglighetsdiagram |[Webbtester](app/monitor-web-app-availability.md) |Vet att din webbapp är uppe |
| Server app perf: svarstider, ... |[Lägg till programstatistik i projektet](app/asp-net.md) eller [Installera AI-statusövervakare på servern](app/monitor-performance-live-website-now.md) (eller skriv din egen kod för att spåra [beroenden)](app/api-custom-events-metrics.md#trackdependency) |Identifiera problem med perf |
| Beroendetelemetri |[Installera AI-statusövervakare på servern](app/monitor-performance-live-website-now.md) |Diagnostisera problem med databaser eller andra externa komponenter |
| Hämta stackspårningar från undantag |[Infoga TrackException-anrop i koden](app/asp-net-exceptions.md) (men vissa rapporteras automatiskt) |Identifiera och diagnostisera undantag |
| Sök loggspårningar |[Lägga till ett loggkort](app/asp-net-trace-logs.md) |Diagnostisera undantag, problem med perf |
| Grunderna i klientanvändning: sidvisningar, sessioner, ... |[JavaScript-initializer på webbsidor](app/javascript.md) |Användningsanalys |
| Anpassade kundmått |[Spåra samtal på webbsidor](app/api-custom-events-metrics.md) |Förbättra användarupplevelsen |
| Anpassade mått för server |[Spåra samtal på servern](app/api-custom-events-metrics.md) |Business Intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Varför är antalet i diagrammen Sök och Mått ojämlika?

[Sampling](app/sampling.md) minskar antalet telemetriobjekt (begäranden, anpassade händelser och så vidare) som faktiskt skickas från appen till portalen. I Sök visas antalet mottagna objekt. I måttdiagram som visar antalet händelser visas antalet ursprungliga händelser som inträffade. 

Varje objekt som överförs `itemCount` bär en egenskap som visar hur många ursprungliga händelser objektet representerar. Om du vill observera sampling i drift kan du köra den här frågan i Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Konfigurera programinsikter

Du kan [skriva PowerShell-skript](app/powershell.md) med Azure Resource Monitor till:

* Skapa och uppdatera application insights-resurser.
* Ställ in prisplanen.
* Hämta instrumenteringsnyckeln.
* Lägg till en måttavisering.
* Lägg till ett tillgänglighetstest.

Du kan inte ställa in en Metrisk Explorer-rapport eller konfigurera kontinuerlig export.

#### <a name="querying-the-telemetry"></a>Fråga telemetrin

Använd [REST API](https://dev.applicationinsights.io/) för att köra [Analytics-frågor.](app/analytics.md)

### <a name="how-can-i-set-an-alert-on-an-event"></a>Hur ställer jag in en avisering på en händelse?

Azure-aviseringar är endast på mått. Skapa ett anpassat mått som korsar ett värdetröskel när händelsen inträffar. Ange sedan en avisering på måttet. Du får en avisering när måttet överskrider tröskelvärdet i båda riktningarna. du får ingen avisering förrän den första korsningen, oavsett om det ursprungliga värdet är högt eller lågt. det finns alltid en latens på några minuter.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Finns det dataöverföringsavgifter mellan en Azure-webbapp och Application Insights?

* Om din Azure-webbapp finns i ett datacenter där det finns en programinsamlingsslutpunkt, debiteras ingen avgift. 
* Om det inte finns någon samlingsslutpunkt i ditt värddatacenter ådrar sig appens telemetri [azure-utgående avgifter](https://azure.microsoft.com/pricing/details/bandwidth/).

Detta beror inte på var din Application Insights-resurs finns. Det beror bara på fördelningen av våra slutpunkter.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan jag skicka telemetri till application insights-portalen?

Vi rekommenderar att du använder våra SDK:er och använder [SDK API](app/api-custom-events-metrics.md). Det finns varianter av SDK för olika [plattformar](app/platforms.md). Dessa SDK-filer hanterar buffring, komprimering, begränsning, återförsök och så vidare. [Inmatningsschemat](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) och [slutpunktsprotokollet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) är dock offentliga.

### <a name="can-i-monitor-an-intranet-web-server"></a>Kan jag övervaka en intranätwebbserver?

Ja, men du måste tillåta trafik till våra tjänster genom antingen brandväggsund undantag eller proxyomdirigeringar.
- QuickPulse (kort)`https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider (applicering)`https://dc.services.visualstudio.com:443` 
- TelemetriChannel`https://dc.services.visualstudio.com:443` 


Granska vår fullständiga lista över tjänster och IP-adresser [här](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Undantag för brandvägg

Låt webbservern skicka telemetri till våra slutpunkter. 

#### <a name="gateway-redirect"></a>Omdirigering av gateway

Dirigera trafik från servern till en gateway på intranätet genom att skriva över slutpunkter i konfigurationen. Om dessa "Endpoint"-egenskaper inte finns i konfigurationen använder dessa klasser standardvärdena nedan i exemplet ApplicationInsights.config. 

Din gateway ska dirigera trafik till vår slutpunkts basadress. I konfigurationen ersätter du `http://<your.gateway.address>/<relative path>`standardvärdena med .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exempel applicationinsights.config med standardslutpunkter:
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
> ApplicationIdProvider är tillgänglig från och med v2.6.0.



#### <a name="proxy-passthrough"></a>Proxy passthrough

Proxygenomströmning kan uppnås genom att konfigurera antingen en maskinnivå eller proxy på programnivå.
Mer information finns i dotnets artikel om [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Exempel web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan jag köra tillgänglighetswebbtester på en intranätserver?

Våra [webbtester](app/monitor-web-app-availability.md) körs på närvaropunkter som distribueras över hela världen. Det finns två lösningar:

* Brandväggsdörr - Tillåt begäranden till servern från [den långa och föränderliga listan över webbtestagenter](app/ip-addresses.md).
* Skriv din egen kod för att skicka regelbundna förfrågningar till servern inifrån intranätet. Du kan köra Visual Studio webbtester för detta ändamål. Testare kan skicka resultaten till Application Insights med hjälp av TrackAvailability() API.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Hur lång tid tar det för telemetri att samlas in?

De flesta Application Insights-data har en svarstid på under 5 minuter. Vissa data kan ta längre tid. vanligtvis större loggfiler. Mer information finns i [serviceavtalet Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md


## <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar

Den här Vanliga frågor och svar från Microsoft är en lista över vanliga frågor om Azure Monitor för behållare. Om du har några ytterligare frågor om lösningen, gå till [diskussionsforumet](https://feedback.azure.com/forums/34192--general-feedback) och skicka dina frågor. När en fråga ofta ställs lägger vi till den i den här artikeln så att den kan hittas snabbt och enkelt.

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Vad representerar *andra processer* under nodvyn?

**Andra processer** är avsedda att hjälpa dig att tydligt förstå orsaken till den höga resursanvändningen på noden. På så sätt kan du skilja användning mellan behållare processer vs icke-containerized processer.

Vilka är dessa **andra processer?** 

Det här är processer som inte är behållare som körs på noden.  

Hur beräknar vi detta?

**Andra processer** = *Total användning från CAdvisor-användning* - *från containerprocess*

De **andra processerna** omfattar:

- Självhanterade eller hanterade Kubernetes processer som inte är behållare 

- Processer för körning av behållare  

- Kubelet  

- Systemprocesser som körs på noden 

- Andra icke-Kubernetes-arbetsbelastningar som körs på nodmaskinvara eller virtuell dator 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Egenskapsvärdena Image och Namn visas inte i när jag frågar tabellen ContainerLog.

För agentversion ciprod12042019 och senare fylls dessa två egenskaper som standard inte i för varje loggrad för att minimera kostnader som uppstår på insamlade loggdata. Det finns två alternativ för att fråga tabellen som innehåller dessa egenskaper med sina värden:

#### <a name="option-1"></a>Alternativ 1 

Gå med i andra tabeller för att inkludera dessa egenskapsvärden i resultatet.

Ändra dina frågor så att de innehåller ```ContainerInventory``` image- och imagetag-egenskaper från tabellen genom att gå med på egenskapen ContainerID. Du kan inkludera egenskapen Name (som ```ContainerLog``` den tidigare visades i tabellen) från kubepodInventory-tabellens containename-fält genom att gå med i egenskapen ContainerID. Detta är det rekommenderade alternativet.

Följande exempel är en detaljerad exempelfråga som förklarar hur du hämtar dessa fältvärden med kopplingar.

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

Aktivera samlingen för dessa egenskaper för alla behållarloggrader.

Om det första alternativet inte är praktiskt på grund av frågeändringar kan ```log_collection_settings.enrich_container_logs``` du aktivera insamlingen av dessa fält igen genom att aktivera inställningen i agentkonfigurationskartan enligt beskrivningen i [konfigurationsinställningarna för datainsamling](insights/container-insights-agent-config.md).

> [!NOTE]
> Det andra alternativet rekommenderas inte med stora kluster som har mer än 50 noder eftersom det genererar API-serveranrop från varje nod i klustret för att utföra den här anrikningen. Det här alternativet ökar också datastorleken för varje insamlad loggrad.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Kan jag visa mätvärden som samlats in i Grafana?

Azure Monitor for containers stöder visningsmått som lagras i logganalysarbetsytan i Grafana-instrumentpaneler. Vi har tillhandahållit en mall som du kan ladda ner från Grafanas [instrumentpanelsdatabas](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) för att komma igång och referera till dig för att lära dig hur du frågar ytterligare data från dina övervakade kluster för att visualisera i anpassade Grafana-instrumentpaneler. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Kan jag övervaka mitt AKS-motorkluster med Azure Monitor för behållare?

Azure Monitor for containers stöder övervakning av behållararbetsbelastningar som distribueras till AKS-motor (tidigare känt som ACS-motor) kluster som finns på Azure. Mer information och en översikt över de steg som krävs för att aktivera övervakning för det här scenariot finns i [Använda Azure Monitor för behållare för AKS-motor](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Varför visas inte data på min Log Analytics-arbetsyta?

Om du inte kan se några data på log analytics-arbetsytan vid en viss tidpunkt varje dag kan du ha nått standardgränsen på 500 MB eller det dagliga tak som anges för att styra mängden data som ska samlas in dagligen. När gränsen är uppfyllda för dagen stoppas och återupptas datainsamlingen först nästa dag. Information om hur du granskar dataanvändningen och uppdateringen till en annan prisnivå baserat på dina förväntade användningsmönster finns [i Logga dataanvändning och -kostnad](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Vilka är behållartillstånden som anges i tabellen ContainerInventory?

Tabellen ContainerInventory innehåller information om både stoppade och löpande behållare. Tabellen fylls i av ett arbetsflöde inuti agenten som frågar dockern efter alla behållare (körs och stoppas) och vidarebefordrar den data som loggar Analytics arbetsyta.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hur löser jag *registreringsfel för saknade prenumerationer?*

Om felet **Saknas Prenumerationsregistrering för Microsoft.OperationsManagement**kan du lösa det genom att registrera resursleverantören **Microsoft.OperationsManagement** i prenumerationen där arbetsytan har definierats. Dokumentationen för hur du gör detta finns [här](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Finns det stöd för RBAC-aktiverade AKS-kluster?

Behållare övervakningslösningen stöder inte RBAC, men det stöds med Azure Monitor för behållare. Sidan lösningsinformation kanske inte visar rätt information i bladen som visar data för dessa kluster.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hur aktiverar jag logginsamling för behållare i kube-systemets namnområde via Helm?

Loggsamlingen från behållare i namnområdet kube-system är inaktiverad som standard. Logginsamling kan aktiveras genom att ställa in en miljövariabel på omsagenten. Mer information finns på [sidan Azure Monitor för behållare](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hur uppdaterar jag omsagenten till den senast utgivna versionen?

Mer information om hur du uppgraderar agenten finns i [Agenthantering](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Hur aktiverar jag flerradsloggning?

Azure Monitor för behållare stöder inte multiradering, men det finns lösningar tillgängliga. Du kan konfigurera alla tjänster att skriva i JSON-format och sedan Docker / Moby kommer att skriva dem som en enda rad.

Du kan till exempel radbrytas som ett JSON-objekt enligt exemplet nedan för ett exempelnod.js-program:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Dessa data ser ut som följande exempel i Azure Monitor för loggar när du frågar efter dem:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

En detaljerad titt på problemet finns i följande [GitHub-länk](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hur löser jag Azure AD-fel när jag aktiverar liveloggar? 

Följande fel kan visas: **Svarsadressen som anges i begäran matchar inte svarsadresserna som konfigurerats\>för programmet: "<program-ID"**. Lösningen för att lösa det finns i artikeln [Så här visar du behållardata i realtid med Azure Monitor för behållare](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Varför kan jag inte uppgradera klustret efter introduktionen?

Om när du har aktiverat Azure Monitor för behållare för ett AKS-kluster tar du bort log analytics-arbetsytan som klustret skickade sina data till när du försöker uppgradera klustret misslyckas det. För att komma runt detta måste du inaktivera övervakning och sedan återaktivera en annan giltig arbetsyta i din prenumeration. När du försöker utföra klusteruppgraderingen igen bör den bearbetas och slutföras.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Vilka portar och domäner måste jag öppna/vitlista för agenten?

Se kraven för [nätverksbrandväggen](insights/container-insights-onboard.md#network-firewall-requirements) för den proxy- och brandväggskonfigurationsinformation som krävs för den behållare agent med Azure, Azure US Government och Azure China 21Vianet-moln.

## <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
Den här Vanliga frågor och svar från Microsoft är en lista över vanliga frågor om Azure Monitor för virtuella datorer. Om du har några ytterligare frågor om lösningen, gå till [diskussionsforumet](https://feedback.azure.com/forums/34192--general-feedback) och skicka dina frågor. När en fråga ofta ställs lägger vi till den i den här artikeln så att den kan hittas snabbt och enkelt.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Kan jag gå ombord till en befintlig arbetsyta?
Om dina virtuella datorer redan är anslutna till en Log Analytics-arbetsyta kan du fortsätta att använda arbetsytan när du går in på Azure Monitor för virtuella datorer, förutsatt att den finns i en av de regioner som stöds [här](insights/vminsights-enable-overview.md#prerequisites).


### <a name="can-i-onboard-to-a-new-workspace"></a>Kan jag gå ombord till en ny arbetsyta? 
Om dina virtuella datorer inte är anslutna till en befintlig Log Analytics-arbetsyta måste du skapa en ny arbetsyta för att lagra dina data. Skapa en ny standardarbetsyta görs automatiskt om du konfigurerar en enda Azure VM för Azure Monitor för virtuella datorer via Azure-portalen.

Om du väljer att använda den skriptbaserade metoden beskrivs de här stegen i artikel [Aktivera Azure Monitor för virtuella datorer med azure powershell- eller resurshanterarens mallartikel.](insights/vminsights-enable-at-scale-powershell.md) 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Vad gör jag om min virtuella dator redan rapporterar till en befintlig arbetsyta?
Om du redan samlar in data från dina virtuella datorer kanske du redan har konfigurerat den för att rapportera data till en befintlig Log Analytics-arbetsyta.  Så länge arbetsytan finns i en av våra regioner som stöds kan du aktivera Azure Monitor för virtuella datorer till den befintliga arbetsytan.  Om arbetsytan du redan använder inte finns i någon av våra regioner som stöds kan du inte gå ombord till Azure Monitor för virtuella datorer just nu.  Vi arbetar aktivt för att stödja ytterligare regioner.


### <a name="why-did-my-vm-fail-to-onboard"></a>Varför kunde min virtuella dator inte vara ombord?
När du ar ombord en virtuell Azure-dator från Azure-portalen inträffar följande:

* En standardarbetsyta för Logganalys skapas om det alternativet har valts.
* Log Analytics-agenten installeras på virtuella Azure-datorer med ett VM-tillägg, om det fastställs att det krävs.  
* Azure Monitor för virtuella datorer Map Dependency Agent är installerad på Virtuella Azure-datorer med hjälp av ett tillägg, om det bestäms att det krävs. 

Under den inbyggda processen kontrollerar vi om det finns status på vart och ett av ovanstående för att returnera en aviseringsstatus till dig i portalen. Konfigurationen av arbetsytan och agentinstallationen tar vanligtvis 5 till 10 minuter. Det tar ytterligare 5 till 10 minuter att visa övervakningsdata i portalen.  

Om du har initierat introduktion och se meddelanden som anger att den virtuella datorn måste vara ombord, kan du tillåta upp till 30 minuter för den virtuella datorn att slutföra processen. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Jag ser inga eller några data i prestandadiagrammen för min virtuella dator
Våra prestandadiagram har uppdaterats för att använda data som lagras i tabellen *InsightsMetrics.*  Om du vill se data i dessa diagram måste du uppgradera för att använda den nya VM Insights-lösningen.  Se våra [vanliga frågor om GA](insights/vminsights-ga-release-faq.md) för ytterligare information.

Om du inte ser prestandadata i disktabellen eller i några av prestandadiagrammen kanske prestandaräknarna inte är konfigurerade på arbetsytan. LÃ¶s problemet genom att köra följande [PowerShell-skript](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell).


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Hur skiljer sig Azure Monitor för virtuella datorer i funktionen från Service Map?
Funktionen Azure Monitor för virtuella datorer kart baseras på Service Map, men har följande skillnader:

* Kartvyn kan nås från vm-bladet och från Azure Monitor för virtuella datorer under Azure Monitor.
* Anslutningarna i kartan är nu klickbara och visar en vy över anslutningsmåttdata på sidopanelen för den valda anslutningen.
* Det finns ett nytt API som används för att skapa kartor för att bättre stödja mer komplexa kartor.
* Övervakade virtuella datorer ingår nu i klientgruppnoden och donutdiagrammet visar andelen övervakade jämfört med oövervakade virtuella datorer i gruppen.  Den kan också användas för att filtrera listan över datorer när gruppen expanderas.
* Övervakade virtuella datorer ingår nu i serverportgruppnoderna och donutdiagrammet visar andelen övervakade jämfört med oövervakade datorer i gruppen.  Den kan också användas för att filtrera listan över datorer när gruppen expanderas.
* Kartformatet har uppdaterats för att vara mer konsekvent med App Map från Application Insights.
* Sidopanelerna har uppdaterats och har inte den fullständiga uppsättningen av integration som stöddes i Service Map - Update Management, Change Tracking, Security och Service Desk. 
* Alternativet för att välja grupper och datorer att mappa har uppdaterats och stöder nu prenumerationer, resursgrupper, Azure-skalningsuppsättningar för virtuella datorer och molntjänster.
* Du kan inte skapa nya servicemappningsmaskingrupper i Azure Monitor för virtuella datorer.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Varför visar mina resultatdiagram streckade linjer?
Detta kan inträffa av några skäl.  I de fall där det finns en lucka i datainsamlingen visar vi linjerna som prickade.  Om du har ändrat dataprovtagningsfrekvensen för de aktiverade prestandaräknarna (standardinställningen är att samla in data var 60:e sekund) kan du se prickade linjer i diagrammet om du väljer ett smalt tidsintervall för diagrammet och samplingsfrekvensen är mindre än Skopans storlek som används i diagrammet (till exempel är samplingsfrekvensen var 10:e minut och varje skopa på sjökortet är 5 minuter).  Om du väljer ett bredare tidsintervall som ska visas bör diagramlinjerna visas som heldragna linjer i stället för punkter i det här fallet.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Stöds grupper med Azure Monitor för virtuella datorer?
Ja, när du har installerat beroendeagenten samlar vi in information från de virtuella datorerna för att visa grupper baserat på prenumeration, resursgrupp, skaluppsättningar för virtuella datorer och molntjänster.  Om du har använt Service Map och har skapat maskingrupper visas även dessa.  Datorgrupper visas också i gruppfiltret om du har skapat dem för arbetsytan som du visar. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hur ser jag detaljerna för vad som driver den 95:e percentillinjen i de sammanlagda prestandadiagrammen?
Som standard sorteras listan så att du visar de virtuella datorer som har det högsta värdet för den 95:e percentilen för det valda måttet, med undantag för det tillgängliga minnesdiagrammet, som visar de datorer som har det lägsta värdet för den 5:e percentilen.  Om du klickar på diagrammet öppnas vyn **Topp N-lista** med lämpligt mått valt.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hur hanterar kartfunktionen dubbla IPs över olika virtuella nätverk och undernät?
Om du duplicerar IP-intervall antingen med virtuella datorer eller Azure-skalningsuppsättningar för virtuella datorer över undernät och virtuella nätverk, kan det leda till att Azure Monitor för virtuella datorer Map visar felaktig information. Detta är ett känt problem och vi undersöker alternativ för att förbättra denna erfarenhet.

### <a name="does-map-feature-support-ipv6"></a>Har kartfunktionen stöd för IPv6?
Kartfunktionen stöder för närvarande endast IPv4 och vi undersöker stöd för IPv6. Vi stöder även IPv4 som är tunnel inuti IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>När jag läser in en karta för en resursgrupp eller annan stor grupp är kartan svår att visa
Även om vi har gjort förbättringar i Map för att hantera stora och komplexa konfigurationer, inser vi att en karta kan ha många noder, anslutningar och nod som fungerar som ett kluster.  Vi är fast beslutna att fortsätta att öka stödet för att öka skalbarheten.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Varför ser nätverksdiagrammet på fliken Prestanda annorlunda ut än nätverksdiagrammet på översiktssidan för Azure VM?

Översiktssidan för en Virtuell Azure visar diagram baserat på värdens mätning av aktivitet i gästdatorn.  För nätverksdiagrammet på Azure VM Översikt visar det bara nätverkstrafik som kommer att faktureras.  Detta inkluderar inte inter-virtuell nätverkstrafik.  Data och diagram som visas för Azure Monitor för virtuella datorer baseras på data från gästdatorn och nätverksdiagrammet visar all TCP/IP-trafik som är inkommande och utgående till den virtuella datorn, inklusive intervirtiska nätverk.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hur mäts svarstiden för data som lagras i VMConnection och visas i anslutningspanelen och arbetsböckerna?

Svarstid är en approximation. Eftersom vi inte instrument koden för ansökan, vet vi inte riktigt när en begäran börjar och när svaret anländer. Istället observerar vi data som skickas på en anslutning och sedan data som kommer tillbaka på den anslutningen. Vår agent håller reda på dessa skickar och tar emot och försöker para ihop dem: en sekvens av skickar, följt av en sekvens av mottar tolkas som en begäran / svar par. Tidpunkten mellan dessa åtgärder är svarstiden. Det kommer att innehålla nätverksfördröjningen och serverbearbetningstiden.

Den här approximationen fungerar bra för protokoll som är begäran/svarsbaserade: en enda begäran går ut på anslutningen och ett enda svar anländer. Detta gäller http(S) (utan pipelining), men inte uppfyllt för andra protokoll.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Finns deras begränsningar om jag finns med i prisplanen för Log Analytics Free?
Om du har konfigurerat Azure Monitor med en Log Analytics-arbetsyta med hjälp av den *kostnadsfria* prisnivån stöder Azure Monitor för virtuella datorer Map-funktionen endast fem anslutna datorer som är anslutna till arbetsytan. Om du har fem virtuella datorer anslutna till en ledig arbetsyta kopplar du från en av de virtuella datorerna och sedan ansluter en ny virtuell dator, den nya virtuella datorn övervakas inte och återspeglas på kartsidan.  

Under det här villkoret kommer du att uppmanas med alternativet **Försök nu** när du öppnar den virtuella datorn och väljer **Insikter** från den vänstra rutan, även efter att den redan har installerats på den virtuella datorn.  Du uppmanas dock inte med alternativ som normalt skulle inträffa om den här virtuella datorn inte var inbyggd i Azure Monitor för virtuella datorer. 


## <a name="next-steps"></a>Nästa steg
Om din fråga inte besvaras här kan du hänvisa till följande forum till ytterligare frågor och svar.

- [Logga Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Allmän feedback på Azure Monitor finns i [feedbackforumet](https://feedback.azure.com/forums/34192--general-feedback).
