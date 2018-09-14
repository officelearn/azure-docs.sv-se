---
title: Azure loggning och granskning | Microsoft Docs
description: Läs mer om hur du kan använda loggningsdata för att få djupa insikter om ditt program.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 93b0a7e382c27cab5f050166ec8fa89fc7cf6b96
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576110"
---
# <a name="azure-logging-and-auditing"></a>Loggning och granskning i Azure

Azure tillhandahåller en mängd olika konfigurerbara säkerhet, granskning och loggning alternativ för att identifiera brister i säkerhetsprinciper och mekanismer. Den här artikeln beskriver generera, samla in och analysera säkerhetsloggar från tjänster som körs på Azure.

> [!Note]
> Vissa rekommendationerna i den här artikeln kan leda till ökad data, nätverk och beräkning Resursanvändning och öka kostnaderna licens eller prenumeration.

## <a name="types-of-logs-in-azure"></a>Typer av loggar i Azure
Molnprogram är komplexa, med alla rörliga delar. -Loggarna ger data för att skydda dina program och drift. Loggar hjälper dig att felsöka problem eller förhindra potentiella som. Och de kan hjälpa att förbättra programmets prestanda eller underhållsfunktioner eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

Azure loggar är indelade i följande typer:
* **Kontroll och hantering loggar** ger information om Azure Resource Manager-skapa, uppdatera och ta bort åtgärder. Mer information finns i [Azure-aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Data-dataplaner loggar** innehåller information om händelser som skapats som en del Azure-Resursanvändning. Exempel på den här typen av loggen är Windows event system, säkerhet, och programloggar i en virtuell dator (VM) och [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) som är konfigurerade via Azure Monitor.

* **Bearbetade händelser** innehåller information om analyserade händelser/aviseringar som har bearbetats för din räkning. Exempel på den här typen är [aviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) där [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) har bearbetats och analyserats din prenumeration och ger kortfattade säkerhetsaviseringar.

I följande tabell visas de viktigaste typerna av loggar som är tillgängliga i Azure:

| Loggkategori | Loggtyp | Användning | Integrering |
| ------------ | -------- | ------ | ----------- |
|[Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Kontrollplanet händelser i Azure Resource Manager-resurser|   Ger insikt i de åtgärder som utförts på resurser i din prenumeration.|    REST API, [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure-diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Frekventa data om användningen av Azure Resource Manager-resurser i prenumerationen|    Innehåller information om åtgärder som utförts av din resurs själva.| Azure Monitor [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Azure AD-rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Loggar och rapporter | Rapporter inloggning användaraktiviteter och information om användare och grupphantering systemaktivitet.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuella datorer och molntjänster](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Windows händelseloggtjänst och Linux Syslog|  Samlar in systemdata och loggningsdata på de virtuella datorerna och överför dessa data till ett lagringskonto om du föredrar.|   Windows (med hjälp av Windows Azure Diagnostics [[WAD](https://docs.microsoft.com/azure/azure-diagnostics)] lagring) och Linux i Azure Monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Med loggningen i Storage, tillhandahåller mätvärden för ett lagringskonto|Ger information om spåra förfrågningar, analyserar användningstrender och diagnostisera problem med ditt lagringskonto.|   REST API eller [klientbibliotek](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Flödesloggar för Nätverkssäkerhetsgruppen (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON-format, visar utgående och inkommande flöden på basis av per regel|Visar information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp.|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Loggar, undantag och anpassad diagnostik|   Innehåller en programprestandaövervakning (APM) tjänst för webbutvecklare på flera plattformar.| REST-API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Bearbeta data / säkerhetsaviseringar|    Aviseringar i Azure Security Center, Azure Log Analytics-aviseringar|   Innehåller säkerhetsinformation och aviseringar.|  REST API: er, JSON|

### <a name="activity-logs"></a>Aktivitetsloggar
[Azure-aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i din prenumeration. Aktivitetsloggar tidigare kallades ”granskningsloggar” eller ”driftloggar” eftersom de rapporterar [kontrollplanet händelser](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) för dina prenumerationer. 

Hjälp med att du fastställa aktivitetsloggar i ”vad, vem, och när” för skrivåtgärder (det vill säga PLACERA, publicera eller ta bort). Aktivitetsloggar också hjälper dig att se status för åtgärden och andra relevanta egenskaper. Aktivitetsloggar inkluderar inte läsåtgärder (GET).

I den här artikeln avser PUT, POST och ta bort alla skrivåtgärder som en aktivitetslogg innehåller på resurser. Du kan exempelvis använda aktivitetsloggarna för att hitta ett fel när du felsöker problem eller för att övervaka hur en användare i organisationen ändrat en resurs.

![Log aktivitetsdiagram](./media/azure-log-audit/azure-log-audit-fig1.png)

Du kan hämta händelser från en aktivitetslogg med hjälp av Azure-portalen [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), PowerShell-cmdlets och [Azure Monitor REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Aktivitetsloggar har 90-dagars data-kvarhållningsperiod.

Integrationsscenarier för en händelse i aktivitetsloggen:

* [Skapa en e-post eller webhook-avisering som utlöses av en händelse i aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Stream till en händelsehubb](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) för inmatning av en tjänst från tredje part eller anpassade analyslösning till exempel Power BI.

* Analysera dem i Power BI med hjälp av den [Power BI-Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Spara den till ett lagringskonto för arkivering eller manuell granskning](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Du kan ange kvarhållningstid (i dagar) med hjälp av loggprofiler.

* Fråga efter och visa den i Azure-portalen.

* Fråga den via PowerShell-cmdleten, Azure CLI eller REST API.

* Exportera aktivitetslogg med loggprofiler till [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Du kan använda ett lagringskonto eller [händelsehubbnamnområde](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) som inte är i samma prenumeration som det som avger loggen. Den konfigurerar inställningen måste ha rätt [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) åtkomst till båda prenumerationerna.

### <a name="azure-diagnostics-logs"></a>Azure-diagnostikloggar
Azure-diagnostikloggar genereras av en resurs som ger omfattande, frekventa data om användningen av den här resursen. Innehållet i de här loggarna varierar efter resurstyp. Till exempel [Windows-händelsesystemloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) är en kategori för diagnostikloggar för virtuella datorer och [blob, tabell och kö loggar](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) finns kategorier för diagnostikloggar för storage-konton. Diagnostikloggar skiljer sig från aktivitetsloggar som ger insikt i de åtgärder som utförts på resurser i din prenumeration.

![Diagram för Azure diagnostics-loggar](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure-diagnostikloggar erbjuder flera alternativ, till exempel Azure portal, PowerShell, Azure CLI och REST-API.

**Integreringsscenarier**

* Sparar dem till en [lagringskonto](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) för granskning eller manuell granskning. Du kan ange kvarhållningstid (i dagar) med hjälp av inställningarna för startdiagnostik.

* [Stream dem till event hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) för inmatning av en tjänst från tredje part eller anpassade analyslösning som [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analysera dem med [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Tjänster som stöds, schema för diagnostikloggar och stöds loggkategorier per resurstyp**


| Tjänst | Schemat och dokumentation | Resurstyp | Kategori |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Log Analytics för belastningsutjämnaren (förhandsversion)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Nätverkssäkerhetsgrupper|[Log Analytics för Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Diagnostikloggning för Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Key Vault-loggar](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Att aktivera och använda Search Traffic Analytics](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Åtkomst till diagnostikloggar för Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Granska<br>Begäranden|
|Azure Data Lake Analytics|[Åtkomst till diagnostikloggar för Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Granska<br>Begäranden|
|Azure Logic Apps|[Anpassat Logic Apps B2B-spårningsschema](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch-diagnostikloggar](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Logganalys för Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Event Hubs-diagnostikloggar](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Jobbet diagnostikloggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Körnings-<br>Redigering|
|Azure Service Bus|[Service Bus-diagnostikloggar](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory-rapportering
Azure Active Directory (Azure AD) innehåller säkerhets-, aktivitets- och granskningsrapporter för en användares katalog. Den [Azure AD-granskningsrapporten](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) hjälper dig att identifiera Privilegierade åtgärder som inträffat under användarens Azure AD-instans. Privilegierade åtgärder omfattar höjning ändras (till exempel skapa en roll eller antalet återställningar av lösenord), ändra principkonfigurationer (till exempel lösenordsprinciper) eller ändringar i directory-konfigurationen (till exempel ändras till federationsinställningar för domän).

Rapporterna ger granskningsposten efter händelsenamn, den användare som utförde åtgärden målresursen påverkas av ändringen, datum och tid (i UTC). Användare kan hämta listan över granskningshändelser för Azure AD via den [Azure-portalen](https://portal.azure.com/), enligt beskrivningen i [visa dina granskningsloggar](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Inkluderade rapporter visas i följande tabell:

| Säkerhetsrapporter | Aktivitetsrapporter | Granskningsrapporter |
| :--------------- | :--------------- | :------------ |
|Inloggningar från okända källor| Programanvändning: sammanfattning| Kataloggranskningsrapport|
|Inloggningar efter flera fel|  Programanvändning: detaljerad||
|Inloggningar från flera geografiska områden|    Instrumentpanel för program||
|Inloggningar från IP-adresser med misstänkt aktivitet|   Kontoetableringsfel||
|Oregelbunden inloggningsaktivitet|    Enskilda användarenheter||
|Inloggningar från potentiellt infekterade enheter|   Enskilda användaraktivitet||
|Användare med avvikande inloggningsaktivitet| Aktivitetsrapport för grupper||
||Aktivitetsrapport över registrering för lösenordsåterställning||
||Lösenordsåterställningsaktivitet|||

Data i de här rapporterna kan användas till att dina program, till exempel säkerhetsinformation och händelsehantering (SIEM) system, granskning och business intelligence-verktyg. Azure AD reporting API: er ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa dessa [API: er](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) från olika programmeringsspråk och verktyg.

Händelser i Azure AD-granskningsrapporten behålls i 180 dagar.

> [!Note]
> Läs mer om kvarhållning av rapporten, [rapportkvarhållningsregler i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Om du är intresserad av att behålla din längre granskningshändelser kan använda Reporting-API för att regelbundet hämta [granskningshändelser](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) i ett separat datalager.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Virtuella datorer, loggar som använder Azure Diagnostics
[Azure-diagnostik](https://docs.microsoft.com/azure/azure-diagnostics) funktion inom Azure som aktiverar insamlingen av diagnostikdata på ett distribuerat program. Du kan använda diagnostiktillägget från flera källor. För närvarande är [Azure webb- och arbetsroller molntjänstroller](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Virtuella datorer, loggar som använder Azure Diagnostics](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineshttpsazuremicrosoftcomdocumentationlearning-pathsvirtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Azure-datorer](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) som kör Microsoft Windows och [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Du kan aktivera Azure Diagnostics på en virtuell dator genom att göra något av följande:

* [Använd Visual Studio för att spåra Azure-datorer](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Konfigurera Azure Diagnostics via fjärranslutning på en Azure-dator](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Använd PowerShell för att konfigurera diagnostik på Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Skapa en virtuell Windows-dator med övervakning och diagnostik med en Azure Resource Manager-mall](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Lagringsanalys
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) loggar och tillhandahåller mätvärden för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot. Loggningen i Storage Analytics är tillgängligt för den [Azure Blob, kö för Azure och Azure Table storage-tjänsterna](https://docs.microsoft.com/azure/storage/storage-introduction). Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst.

Du kan använda den här informationen för att övervaka enskilda begäranden och diagnostisera problem med en lagringstjänst. Förfrågningar loggas på basis av bästa prestanda. Loggposter skapas endast om det finns förfrågningar mot tjänsteslutpunkt. Om ett storage-konto har aktivitet i dess blob-slutpunkt, men inte i dess tabell- eller slutpunkter, skapas till exempel loggar som hör till Blob storage-tjänsten.

Om du vill använda Storage Analytics, aktivera den individuellt för varje tjänst som du vill övervaka. Du kan aktivera den i den [Azure-portalen](https://portal.azure.com/). Mer information finns i [övervaka ett lagringskonto i Azure-portalen](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Du kan använda åtgärden Ange egenskaper för filtjänsten för att aktivera Storage Analytics individuellt för varje tjänst.

Sammanställda data lagras i en välkänd blob (för loggning) och välkänd tabeller (för mått) som du kan komma åt genom att använda Blob storage-tjänsten och Table storage-tjänsten API: er.

Lagringsanalys har en 20 terabyte (TB) gräns för mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Alla loggar lagras i [blockblobbar](https://docs.microsoft.com/azure/storage/storage-analytics) i en behållare med namnet $logs, som skapas automatiskt när du aktiverar Storage Analytics för ett lagringskonto.

> [!Note]
> * Läs mer om fakturering och datalagringsprinciper [Lagringsanalys och fakturering](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Läs mer om lagringskontogränser [skalbarhets- och prestandamål i Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Lagringsanalys loggar följande typer av autentiserade och anonyma begäranden:

| Autentiserad  | Anonym|
| :------------- | :-------------|
| Lyckade begäranden | Lyckade begäranden |
|Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel | Begäranden som använder en signatur för delad åtkomst, inklusive misslyckade och lyckade förfrågningar |
| Begäranden som använder en signatur för delad åtkomst, inklusive misslyckade och lyckade förfrågningar |Timeout-fel för både klient och server |
|   Begäranden till analytics-data |    Misslyckade GET-begäranden med felkoden 304 (har inte ändrats) |
| Begäranden som görs av Storage Analytics, till exempel log skapas eller tas bort, loggas inte. En fullständig lista över data som loggats dokumenteras i [Lagringsanalys loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys loggformat](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Alla övriga misslyckade anonyma förfrågningar loggas inte. En fullständig lista över data som loggats dokumenteras i [Lagringsanalys loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys loggformat](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Azures nätverksloggar
Nätverket loggning och övervakning i Azure är omfattande och består av två olika kategorier:

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher): scenariobaserade nätverksövervakning tillhandahålls med funktionerna i Network Watcher. Den här tjänsten innefattar paketinsamling, nästa hopp, IP-flöde verifiera säkerhetsgruppvy, NSG-flödesloggar. Scenariot på övervakning ger en heltäckande vy av nätverksresurser, till skillnad från enskilda resource nätverksövervakning.

* [Resursövervakning](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring): nivån Resursövervakning består av fyra funktioner, diagnostikloggar, mätvärden, felsökning och resurshälsa. Alla dessa funktioner bygger på nätverksnivå för resursen.

![Azures nätverksloggar](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher är en regional tjänst som hjälper dig att övervaka och diagnostisera villkor på nätverksnivå, till och från Azure. Nätverksdiagnostik och visualiseringsverktyg för Network Watcher hjälper dig att förstå, diagnostisera och få information om ditt nätverk i Azure.

### <a name="network-security-group-flow-logging"></a>Flödesloggar för Nätverkssäkerhetsgruppen

[NSG-flödesloggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) är en funktion i Network Watcher som du kan använda för att visa information om ingående och utgående IP-trafik via en NSG. Dessa flödesloggar skrivs i JSON-format och visa:
* Utgående och inkommande flöden på basis av per regel.
* Nätverkskortet som flödet som gäller för.
* 5-tuppel information om flödet: käll- eller mål-IP och port för käll- eller målservrar protokollet.
* Om trafik tillåts eller nekas.

Även om flödesloggarna target NSG: er, visas de inte på samma sätt som andra loggarna. Flödesloggar lagras endast i ett lagringskonto.

Av samma principer för kvarhållning av säkerhetskopior som visas på andra loggar gäller flödesloggar. Loggar har en bevarandeprincip som du kan ange mellan 1 dag och 365 dagar. Om en bevarandeprincip inte är inställd bevaras loggarna för evigt.

**Diagnostikloggar**

Periodiska och spontant händelser skapas av nätverksresurser och loggas i storage-konton och skickas till en händelsehubb eller Log Analytics. Loggarna ge insikter om hälsotillståndet för en resurs. De kan visas i verktyg som Power BI och Log Analytics. Läs hur du visar diagnostikloggar i [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Diagnostikloggar](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnostikloggar är tillgängliga för [belastningsutjämnaren](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), vägar, och [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher får en diagnostics-loggar vy. Den här vyn innehåller alla nätverksresurser som har stöd för diagnostik loggning. Du kan aktivera och inaktivera nätverksresurser smidigt och snabbt från den här vyn.


Förutom funktionerna som tidigare nämnts loggning har Network Watcher för tillfället följande funktioner:
- [Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): ger en på nätverksnivå vy som visar de olika anslutningarna och associationer mellan nätverksresurser i en resursgrupp.

- [Variabla infångade paket](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): samlar in paketdata till och från en virtuell dator. Avancerade alternativ för filtrering och justera kontroller, till exempel inställningar för tid och storlek begränsning, ger flexibiliteten. Paketdata kan lagras i en blob store eller på den lokala disken i *.cap* filformat.

* [IP-flöde verifiering](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): kontrollerar om ett paket tillåts eller nekas baserat på flow information 5-tuppel paket parametrar (det vill säga mål-IP, käll-IP, målport, källport och protokoll). Om paketet nekas av en säkerhetsgrupp, returneras regeln och grupp som nekade paketet.

* [Nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Anger nästa hopp för paket som vidarebefordras i Azure nätverksinfrastruktur, så att du kan diagnostisera eventuella felkonfigurerad användardefinierade vägar.

* [Säkerhetsgruppvy](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): hämtar de effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.

* [Virtuell nätverksgateway och anslutning felsökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): hjälper dig att felsöka virtuella nätverksgatewayer och anslutningar.

* [Nätverks-prenumerationsbegränsningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits): gör att du kan visa nätverksresursanvändning mot gränser.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar APM-tjänst för webbutvecklare på flera plattformar. Du kan använda den för att övervaka live-webbprogram. Den identifierar automatiskt prestandaavvikelser. Den inkluderar kraftfulla analysverktyg för att hjälpa dig diagnosticera problem och förstå vad användare faktiskt gör med din app.

Application Insights är utformad för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

Det fungerar för appar på en mängd olika plattformar, inklusive .NET, Node.js och J2EE, oavsett om de är finns lokalt eller i molnet. Den integrerar med din DevOps-process och har kopplingspunkter med olika utvecklingsverktyg.

![Application Insights-diagram](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights är avsett för utvecklingsteamet och gör det lättare att förstå hur appen fungerar och hur den används. Tjänsten övervakar:

* **Begär frekvens, svarstider och Felfrekvens**: ta reda på vilka sidor som är mest populära vid vilka tidpunkter på dagen, och var dina användarna finns. Se vilka sidor som fungerar bäst. Om din svarstider och Felfrekvens går hög när det finns fler begäranden, kanske ett resourcing problem.

* **Beroendefrekvens, svarstider och Felfrekvens**: ta reda på om externa tjänster gör systemet långsammare.

* **Undantag**: analysera aggregerad statistik, eller Välj specifika instanser och öka detaljnivån i stackspårningen och relaterade begäranden. Både server- och webbläsarundantag rapporteras.

* **Sidvyer och inläsningsprestanda**: hämta rapporter från användarnas webbläsare.

* **AJAX-anrop**: hämta webbsidan frekvens, svarstider och Felfrekvens.

* **Antal användare och sessioner**.

* **Prestandaräknare**: hämta data från din Windows- eller Linux serverdatorer, till exempel processor, minne, och nätverkets användning.

* **Värddiagnostik**: hämta data från Docker eller Azure.

* **Diagnostik spårningsloggar**: hämta data från din app, så att du kan jämföra spårningshändelser med begäranden.

* **Anpassade händelser och mått**: hämta data som du skriver själv i klient- eller serverkoden för att spåra affärshändelser, t.ex. sålda artiklar eller vunna spel.

I följande tabell listar och beskriver integrationsscenarier:

| Integreringsscenario | Beskrivning |
| --------------------- | :---------- |
|[Programkarta](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Komponenterna i din app, med viktiga mätvärden och aviseringar.||
|[Diagnostik söka för instans data](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Sök efter och filtrera händelser, till exempel begäranden, undantag, beroendeanrop, loggspårningar och sidvyer.||
|[Metrics Explorer för aggregerade data](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Utforska, filtrera och segmentera aggregerade data, till exempel begärande-, fel- och undantagsfrekvens, svarstider och sidinläsningstider.||
|[Instrumentpaneler](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Kombinera data från flera resurser och dela med andra. Perfekt för program med flera komponenter och för kontinuerlig visning i teamutrymmet.||
|[Live Metrics Stream](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|När du distribuerar en ny version kan du titta på dessa prestandaindikatorer som visas i realtid för att kontrollera att allt fungerar som förväntat.||
|[Analys](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Besvara svåra frågor om appens prestanda och användning med hjälp av det här kraftfulla frågespråket.||
|[Automatiska och manuella aviseringar](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatiska aviseringar anpassa appens normala telemetrimönster telemetri och utlöses när det finns något utanför det vanliga mönstret. Du kan också ställa in aviseringar på särskilda nivåer med anpassade mätvärden eller standardmätvärden.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Visa prestandadata i koden. Gå till kod från stackspårningar.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrera användningsmätvärden med annan Business Intelligence.||
|[REST API](https://dev.applicationinsights.io/)|Skriv kod för att köra frågor mot dina mätvärden och rådata.||
|[Löpande export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Massexportera rådata till lagring när det kommer fram.||

### <a name="azure-security-center-alerts"></a>Azure Security Center-aviseringar
Hotidentifieringen i Azure Security Center sker genom automatisk insamling av säkerhetsinformation från dina Azure-resurser, nätverket och anslutna partnerlösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas. Mer information finns i [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Azure Security Center-diagram](./media/azure-log-audit/azure-log-audit-fig7.png)

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Den gäller genombrott i stora mängder data och [maskininlärning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tekniker för att utvärdera händelser i hela molninfrastrukturen. På så vis upptäcks hot som skulle vara omöjliga att identifiera med manuella metoder och att förutsäga utvecklingen av nya attacker. Dessa säkerhetsanalyser omfattar:

* **Integrerad hotinformation**: söker efter kända illvilliga aktörer genom att tillämpa global hotinformation från Microsoftprodukter och tjänster, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa flöden används.

* **Beteendeanalys**: tillämpar kända mönster för att identifiera skadligt beteende.

* **Avvikelseidentifiering**: använder statistisk profilering för att skapa en historisk baslinje. Den här typen av identifiering varnar vid avvikelser från upprättade baslinjer som matchar vektorn för ett potentiellt angrepp.

Många säkerhetsåtgärder och incidenter team beroende av en SIEM-lösning som startpunkt för sortering och undersökning av säkerhetsaviseringar. Du kan synkronisera aviseringar i Security Center och virtuella datorer säkerhetshändelser som samlas in av Azure diagnostics och granska loggar med din Log Analytics eller SIEM-lösning i nära realtid med Azure Log Integration.

## <a name="log-analytics"></a>Log Analytics 

Log Analytics är en tjänst i Azure som hjälper dig att samla in och analysera data som genereras av resurser i molnet och lokala miljöer. Det ger dig realtidsinsikter med integrerad sökning och anpassade instrumentpaneler för snabb analys av miljontals poster över alla dina arbetsbelastningar och servrar, oavsett deras fysiska plats.

![Log Analytics-diagram](./media/azure-log-audit/azure-log-audit-fig8.png)

I centrum av de Log Analytics är Log Analytics-arbetsyta som ligger i Azure. Log Analytics samlar in data i arbetsytan från anslutna källor genom att konfigurera datakällor och lägga till lösningar i din prenumeration. Datakällor och lösningar som var och skapa olika posttyper, var och en med sin egen uppsättning egenskaper. Men datakällor och lösningar kan fortfarande analyseras tillsammans i förfrågningar till arbetsytan. Den här funktionen kan du använda samma verktyg och metoder för att arbeta med en mängd data som samlats från olika källor.

Anslutna datakällor är datorerna och andra resurser som genererar data som samlas in av Log Analytics. Källor kan omfatta agenter som är installerade på [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) och [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) datorer som ansluter direkt, eller agenter i [en ansluten System Center Operations Manager-hanteringsgrupp](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Log Analytics kan också samla in data från en [Azure storage-konto](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Datakällor](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) finns olika typer av data som samlas in från varje anslutna källa. Källor inkludera händelser och [prestandadata](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) från [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) och Linux-agenter förutom källor som [IIS-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) och [anpassade textloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Du kan konfigurera varje datakälla som du vill samla in och konfigurationen skickas automatiskt till varje ansluten källa.

Det finns fyra sätt att [samla in loggar och mått för Azure-tjänster](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):
* Azure Diagnostics-data direkt till Log Analytics (**diagnostik** i tabellen nedan)

* Azure Diagnostics-data till Azure-lagring till Log Analytics (**Storage** i tabellen nedan)

* Anslutningsappar för Azure-tjänster (**Connector** i tabellen nedan)

* Skript för att samla in och publicera sedan data till Log Analytics (tomma celler i följande tabell och för tjänster som inte visas)

| Tjänst | Resurstyp | Logs | Mått | Lösning |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostik|Diagnostik|    [Azure-program](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Koppling|  Koppling|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Anslutningsapp (förhandsversion)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Azure Automation-konton| Microsoft.Automation/<br>AutomationAccounts|    Diagnostik||       [Mer information](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Azure Batch-konton|  Microsoft.Batch/<br>batchAccounts|  Diagnostik|    Diagnostik||
|Klassiska molntjänster||       Storage||       [Mer information](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>konton|       Diagnostik|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>konton|   Diagnostik|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>konton|   Diagnostik|||
|Azure Event Hub-namnområdet| Microsoft.EventHub/<br>Namnområden|  Diagnostik|    Diagnostik||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnostik||
|Azure Key Vault|   Microsoft.KeyVault/<br>Valv|  Diagnostik  || [Key Vault-analys](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>belastningsutjämnare|    Diagnostik|||
|Azure Logic Apps|  Microsoft.Logic/<br>Arbetsflöden|  Diagnostik|    Diagnostik||
||Microsoft.Logic/<br>integrationAccounts||||
|Nätverkssäkerhetsgrupper|   Microsoft.Network/<br>networksecuritygroups|Diagnostik||   [Azure Network Security Group analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Recovery-valv|   Microsoft.RecoveryServices/<br>Valv|||[Azure Recovery Services-analys (förhandsversion)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Söktjänster|   Microsoft.Search/<br>searchServices|    Diagnostik|    Diagnostik||
|Service Bus-namnområde| Microsoft.ServiceBus/<br>Namnområden|    Diagnostik|Diagnostik|    [Service Bus-analys (förhandsversion)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric-analys (förhandsversion)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servrar /<br>databaser||       Diagnostik||
||Microsoft.Sql/<br>servrar /<br>elasticPools||||
|Storage|||         Skript| [Azure Storage-analys (förhandsversion)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft.Compute/<br>virtuella datorer|  Anknytning|  Anknytning||
||||Diagnostik||
|Skalningsuppsättningar för virtuella datorer|    Microsoft.Compute/<br>virtuella datorer    ||Diagnostik||
||Microsoft.Compute/<br>virtualMachineScaleSets /<br>virtuella datorer||||
|Server webbgrupper|Microsoft.Web/<br>servergrupper||   Diagnostik
|Websites|  Microsoft.Web/<br>Platser ||      Diagnostik|    [Mer information](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>platser /<br>fack|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Loggintegrering med lokala SIEM-system
Med [Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324), du kan integrera loggarna från dina Azure-resurser med din lokala SIEM-system.

![Log Integration diagram](./media/azure-log-audit/azure-log-audit-fig9.png)

Loggintegrering samlar in Azure-diagnostik från din Windows-datorer, Azure-aktivitetsloggar, aviseringar i Azure Security Center och Azure-resurs av lagringsproviderns loggfiler. Den här integreringen ger en enhetlig instrumentpanel för alla dina tillgångar, oavsett om de är lokala eller i molnet, så att du kan aggregera korrelera, analysera och varna för säkerhetshändelser.

Loggintegrering stöder för närvarande integreringen av Azure-aktivitetsloggar, Windows-händelseloggar från Windows-datorer med din Azure-prenumeration, Azure Security Center-aviseringar, Azure-diagnostikloggar och Azure AD-granskningsloggar.

| Loggtyp | Log Analytics stöd för JSON (Splunk ArcSight och IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Granskningsloggar för Azure AD|   Ja|
|Aktivitetsloggar| Ja|
|Security Center-aviseringar |Ja|
|Diagnostikloggar (resurs-loggarna)|  Ja|
|VM-loggar|   Ja, via vidarebefordrade händelser och inte via JSON|

[Kom igång med Azure Log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): den här självstudiekursen beskriver hur du installerar Azure Log Integration och integrera loggar från Azure storage, Azure-aktivitetsloggar och aviseringar i Azure Security Center Azure AD granskningsloggar.

Integrationsscenarier för SIEM:

* [Partner konfigurationssteg](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): det här blogginlägget visar hur du konfigurerar Azure Log Integration att arbeta med partnerlösningar Splunk, HP ArcSight och IBM QRadar.

* [Azure Log Integration FAQ](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): den här artikeln ger svar på frågor om Azure Log Integration.

* [Integrera aviseringar i Security Center med Azure Log Integration](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): den här artikeln beskrivs hur du synkroniserar aviseringar i Security Center, virtuell dator säkerhetshändelser som samlas in av Azure-diagnostikloggar och Azure granskningsloggar med Log Analytics eller SIEM lösning.

## <a name="next-steps"></a>Nästa steg

- [Granskning och loggning](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): skydda data genom att upprätthålla synlighet och svara snabbt på rätt tid säkerhetsaviseringar.

- [Security loggning och granskningsloggen samling i Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): tillämpa dessa inställningar för att säkerställa att dina Azure-instanser kan samla in rätt säkerhet och granskning loggarna.

- [Konfigurera granskningsinställningar för en webbplatssamling](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Om du är administratör för en webbplatssamling kan hämta historiken för enskilda användare, åtgärder och historik över åtgärder som vidtagits under ett visst datumintervall. 

- [Sök igenom granskningsloggen i Office 365 säkerhets- och Efterlevnadscenter](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Använd Office 365 säkerhets- och Efterlevnadscenter för att söka enhetlig granskningsloggen och visa användar- och aktivitet i din Office 365-organisation.


