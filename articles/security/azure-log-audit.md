---
title: Azure-loggning och granskning | Microsoft Docs
description: Läs mer om hur du kan använda loggning av data och få djupa insikter om ditt program.
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
ms.openlocfilehash: e4144ca0d87abda3d9f8de47e56af59d0e4af312
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938374"
---
# <a name="azure-logging-and-auditing"></a>Loggning och granskning i Azure

Azure tillhandahåller en mängd olika konfigurerbara säkerhet granskning och loggning alternativ som hjälper dig att identifiera brister i säkerhetsprinciper och mekanismer. Den här artikeln beskrivs genererar, samla in och analysera säkerhetsloggar från tjänster i Azure.

> [!Note]
> Vissa rekommendationerna i den här artikeln kan leda till ökad data, nätverk eller beräkning Resursanvändning och öka kostnaderna licens eller prenumeration.

## <a name="types-of-logs-in-azure"></a>Typer av loggar i Azure
Molnprogram är komplicerade med många rörliga delar. Loggar tillhandahåller data för att skydda dina program igång. Loggar hjälper dig att felsöka problem eller förhindra potentiella de. Och de kan förbättra programmets prestanda eller underhålla eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

Azure loggar är indelade i följande typer:
* **Kontroll och hantering loggar** innehåller information om att skapa Azure Resource Manager-, UPDATE- och DELETE-åtgärder. Mer information finns i [Azure aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Data plan loggar** innehåller information om händelser som aktiverats som en del Azure Resursanvändning. Exempel på den här typen av loggen är Windows händelse system, säkerhet, och program som loggar in på en virtuell dator (VM) och [diagnostik loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) som konfigureras via Azure-Monitor.

* **Bearbetade händelser** innehåller information om analyserade/aviseringar som har bearbetats å dina vägnar. Exempel på den här typen är [Azure Security Center-aviseringar](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) där [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) har bearbetats analyseras och din prenumeration och ger kortare säkerhetsaviseringar.

I följande tabell visas de viktigaste typerna av loggar som är tillgängliga i Azure:

| Loggen kategori | Loggtyp | Användning | Integrering |
| ------------ | -------- | ------ | ----------- |
|[Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Kontroll-plan händelser på Azure Resource Manager-resurser|   Ger inblick i åtgärder som utfördes på resurser i din prenumeration.|    REST-API, [Azure Övervakare](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure-diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Ofta data om handhavandet av Azure Resource Manager resurserna i prenumerationen|    Ger inblick i åtgärder som utförts av din resurs sig själv.| Övervakare för Azure [dataström](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Azure AD-rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Loggar och rapporter | System aktivitetsinformation om användare och grupphantering och rapporter inloggning användaraktiviteter.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuella datorer och molntjänster](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-quick-collect-azurevm)|Tjänsten Windows Event Log- och Linux Syslog|    Samlar in systemdata och loggningsdata på virtuella datorer och överför data till ett lagringskonto som du väljer.|   Windows (med hjälp av Windows Azure-diagnostik [[BOMULLSTUSS](https://docs.microsoft.com/azure/azure-diagnostics)] storage)- och Linux i Azure-Monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Loggning av lagring, tillhandahåller data för mått för ett lagringskonto|Ger inblick i trace-begäranden, analyserar användningstrender och diagnostiserar problem med ditt lagringskonto.|   REST API eller [klientbibliotek](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Nätverkssäkerhetsgrupp (NSG) flöde loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON-format, visar utgående och inkommande flöden på grundval av per regel|Visar information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp.|[Azure Nätverksbevakaren](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Loggar, undantag och anpassade diagnostik|   Innehåller en programprestandaövervakning (APM)-tjänsten för webbutvecklare på flera plattformar.| REST API [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Bearbeta data / säkerhetsaviseringar|    Azure Security Center-aviseringar, Azure logganalys aviseringar|   Innehåller information om säkerhet och aviseringar.|  REST API: er, JSON|

### <a name="activity-logs"></a>Aktivitetsloggar
[Azure aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om de åtgärder som utfördes på resurser i din prenumeration. Aktivitetsloggar tidigare kallas ”granskningsloggar” eller ”operativa loggar” eftersom de rapporterar [kontroll-plan händelser](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) för dina prenumerationer. 

Aktiviteten loggar hjälp dig att avgöra den ”vad som, och när” för skrivåtgärder (det vill säga PLACERA, efter eller ta bort). Aktiviteten loggar också hjälper dig att förstå statusen för åtgärden och andra relevanta egenskaper. Aktivitetsloggar omfattar inte läsåtgärder (GET).

I den här artikeln avser PUT, POST och DELETE alla skrivåtgärder som en aktivitetsloggen innehåller på resurser. Du kan till exempel använda aktivitetsloggarna att hitta ett fel när du felsöker problem eller för att övervaka hur en användare i din organisation ändrades en resurs.

![Aktiviteten loggen diagram](./media/azure-log-audit/azure-log-audit-fig1.png)

Du kan hämta händelser från en aktivitetslogg med hjälp av Azure portal [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), PowerShell-cmdlets och [REST-API för Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Aktivitetsloggar har 19 dagars data-Bevarandeperiod.

Integrationsscenarier för en aktivitet logga en händelse:

* [Skapa en e-post eller webhook avisering som utlöses av en aktivitet händelselogg](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Strömma till en händelsehubb](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) för införandet av en tjänst från tredje part eller anpassade analytics lösning, till exempel PowerBI.

* Analysera i PowerBI med den [PowerBI Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Spara den till ett lagringskonto för arkivering eller Manuell kontroll](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Du kan ange tiden för datakvarhållning (i dagar) med hjälp av loggen profiler.

* Fråga efter och visa den i Azure-portalen.

* Fråga den via PowerShell-cmdleten, Azure CLI eller REST API.

* Exportera aktivitetsloggen med log-profiler till [logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Du kan använda ett lagringskonto eller [event hub namnområde](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) som inte är i samma prenumeration som det som avger loggen. Den som konfigurerar inställningen måste ha rätt [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) åtkomst till båda prenumerationer.

### <a name="azure-diagnostics-logs"></a>Azure-diagnostikloggar
Azure diagnostics loggar orsakat av en resurs som ger omfattande, ofta data om användningen av den här resursen. Innehållet i de här loggarna varierar beroende på resurstypen. Till exempel [Windows-händelsesystemloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) är en kategori av loggar för diagnostik för virtuella datorer, och [blob-, tabell- och kön loggar](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) finns kategorier av diagnostik loggar för storage-konton. Diagnostik loggar skiljer sig från aktiviteten loggarna, vilket ger kunskaper om de åtgärder som utfördes på resurser i din prenumeration.

![Azure diagnostics loggar diagram](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure diagnostics loggar erbjuder flera konfigurationsalternativ, till exempel Azure-portalen, PowerShell, Azure CLI och REST-API.

**Integrationsscenarier**

* Spara dem på en [lagringskonto](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) för granskning eller Manuell kontroll. Du kan ange tiden för datakvarhållning (i dagar) med hjälp av inställningarna för webbprogramdiagnostik.

* [Strömma dem i händelsehubbar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) för införandet av en tjänst från tredje part eller anpassade analytics-lösning som [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analysera dem med [logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Tjänster som stöds, schemat för diagnostik loggar och stöds loggen kategorier per resurstyp**


| Tjänst | Schemat och dokumentation | Resurstyp | Kategori |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Log Analytics för belastningsutjämnaren (förhandsgranskning)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Nätverkssäkerhetsgrupper|[Log Analytics för Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Diagnostikloggning för Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Key Vault-loggar](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Att aktivera och använda Sök trafik Analytics](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Diagnostik för åtkomstloggar för Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Granska<br>Begäranden|
|Azure Data Lake Analytics|[Diagnostik för åtkomstloggar för Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Granska<br>Begäranden|
|Azure Logic Apps|[Anpassat Logic Apps B2B-spårningsschema](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch diagnostik loggar](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Log Analytics för Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Händelseloggar hubbar diagnostik](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Loggar för jobbet diagnostik](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Körnings-<br>Redigering|
|Azure Service Bus|[Service Bus diagnostik loggar](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory-rapportering
Azure Active Directory (AD Azure) innehåller säkerhet, aktiviteten och granskningsrapporter för en användares katalog. Den [Azure AD-kontrollrapport](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) hjälper dig att identifiera Privilegierade åtgärder som uppstått i användarens Azure AD-instans. Privilegierade åtgärder omfattar höjning ändringar (till exempel skapa användarrollen eller återställning av lösenord), ändra principkonfigurationer (till exempel lösenordsprinciper) eller ändringar av katalogkonfiguration (till exempel ändringar i federation domäninställningar).

Med rapporterna får granskningsposten för händelsenamnet användaren som utförde åtgärden målresursen påverkas av ändringen, samt datum och tid (UTC). Användare kan hämta listan över granskningshändelser för Azure AD via den [Azure-portalen](https://portal.azure.com/), enligt beskrivningen i [visa din granskningsloggar](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Inkluderade rapporter visas i följande tabell:

| Säkerhetsrapporter | Aktivitetsrapporter | Granskningsrapporter |
| :--------------- | :--------------- | :------------ |
|Inloggningar från okända källor| Programanvändning: sammanfattning| Kataloggranskningsrapport|
|Inloggningar efter flera fel|  Programanvändning: detaljerad||
|Inloggningar från flera geografiska områden|    Instrumentpanel för program||
|Inloggningar från IP-adresser med misstänkt aktivitet|   Kontoetableringsfel||
|Oregelbunden inloggningsaktivitet|    Enskilda användarenheter||
|Inloggningar från potentiellt infekterade enheter|   Användaraktivitet för enskilda||
|Användare med avvikande inloggningsaktivitet| Aktivitetsrapport för grupper||
||Aktivitetsrapport över registrering för lösenordsåterställning||
||Lösenordsåterställningsaktivitet|||

Data i dessa rapporter kan vara användbara för ditt program, till exempel Security Information and Event Management SIEM ()-system, granskning och business intelligence-verktyg. Azure AD reporting API: er ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa dessa [API: er](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) från olika programmeringsspråk språk och verktyg.

Händelser i Azure AD granska rapporten finns kvar i 180 dagar.

> [!Note]
> Läs mer om rapporten kvarhållning [rapportkvarhållningsregler i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Om du är intresserad av att behålla din granskningshändelser längre använder Reporting-API för att regelbundet hämtar [granskningshändelser](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) i ett separat datalager.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Loggar för virtuell dator som använder Azure-diagnostik
[Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) är funktion i Azure som möjliggör insamling av diagnostikdata på ett distribuerat program. Du kan använda diagnostik tillägg från någon av flera olika källor. Stöds för närvarande är [Azure webb- och arbetsroller molntjänstroller](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Loggar för virtuell dator som använder Azure-diagnostik](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineshttpsazuremicrosoftcomdocumentationlearning-pathsvirtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Virtuella Azure-datorer](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) som kör Microsoft Windows och [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Du kan aktivera Azure-diagnostik på en virtuell dator genom att göra något av följande:

* [Använd Visual Studio för att spåra virtuella Azure-datorer](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Ställa in Azure-diagnostik via fjärranslutning på en virtuell Azure-dator](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Använd PowerShell för att ställa in diagnostik på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Skapa en virtuell Windows-dator med övervakning och diagnostik med en Azure Resource Manager-mall](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Lagringsanalys
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) loggar och tillhandahåller data för mått för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot. Storage Analytics loggning är tillgänglig för den [Azure Blob och Azure Queue Azure Table storage-tjänster](https://docs.microsoft.com/azure/storage/storage-introduction). Storage Analytics loggar detaljerad information om lyckade och misslyckade förfrågningar till en storage-tjänst.

Du kan använda den här informationen för att övervaka enskilda begäranden och diagnostisera problem med en storage-tjänst. Begäranden loggas för bästa prestanda. Loggposter skapas bara om det finns begäranden som görs mot tjänstslutpunkten. Till exempel om ett lagringskonto har aktivitet i sin slutpunkt för blob men inte i de tabell eller kön slutpunkterna, skapas loggar som hör till Blob storage-tjänst.

Om du vill använda Storage Analytics, aktivera den separat för varje tjänst som du vill övervaka. Du kan aktivera den i den [Azure-portalen](https://portal.azure.com/). Mer information finns i [övervaka ett lagringskonto i Azure portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Du kan använda åtgärden Ange egenskaper för tjänsten för att aktivera Storage Analytics individuellt för varje tjänst.

Sammanställda data lagras i en välkänd blob (för loggning) och välkända tabeller (för statistik), och du kan komma åt genom att använda Blob storage-tjänst och Table storage-tjänsten API: er.

Storage Analytics har en 20 terabyte (TB) gräns på mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Alla loggar lagras i [blockblobbar](https://docs.microsoft.com/azure/storage/storage-analytics) i en behållare med namnet $logs, som skapas automatiskt när du aktiverar Storage Analytics för ett lagringskonto.

> [!Note]
> * Läs mer om fakturering och datakvarhållningsprinciper [Storage Analytics och fakturering för](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Läs mer om lagringskontogränser [skalbarhets- och prestandamål för Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Storage Analytics loggar följande typer av autentiserade och anonyma begäranden:

| Autentiserad  | Anonym|
| :------------- | :-------------|
| Lyckade begäranden | Lyckade begäranden |
|Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel | Begäranden som använder en signatur för delad åtkomst, inklusive misslyckade och lyckade begäranden |
| Begäranden som använder en signatur för delad åtkomst, inklusive misslyckade och lyckade begäranden |Timeout-fel för både klient och server |
|   Begäranden till analysdata |    Misslyckade GET-begäranden med felkoden 304 (ändra inte) |
| Förfrågningar som görs av Storage Analytics, till exempel loggen skapas eller tas bort, loggas inte. En fullständig lista över data som loggats dokumenteras i [Storage Analytics loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformatet](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Alla andra misslyckade anonyma begäranden har inte loggats. En fullständig lista över data som loggats dokumenteras i [Storage Analytics loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformatet](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Azure-nätverk loggar
Loggning och nätverksövervakning i Azure är omfattande och omfattar två kategorier:

* [Nätverk Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher): scenariobaserade nätverksövervakning tillhandahålls med funktionerna i Nätverksbevakaren. Den här tjänsten innehåller paketinsamling, nästa hopp, IP-flöde Kontrollera NSG flödet loggar i gruppvyn säkerhet. Scenariot nivån övervakning innehåller en heltäckande vy av nätverksresurser i stället för enskilda resurs nätverksövervakning.

* [Resursövervakning](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring): nivån Resursövervakning består av fyra funktioner, diagnostik loggar, mått, felsökning och resurshälsa. Alla dessa funktioner är byggda på resursen nätverksnivån.

![Azure-nätverk loggar](./media/azure-log-audit/azure-log-audit-fig4.png)

Nätverksbevakaren är en regionala tjänst som gör att du kan övervaka och diagnostisera villkor på nätverket scenariot nivå, till och från Azure. Nätverksdiagnostik och visualiseringsverktyg som finns tillgängliga med Nätverksbevakaren hjälper dig att förstå, diagnostisera och få insyn i nätverket i Azure.

### <a name="network-security-group-flow-logging"></a>Nätverkssäkerhetsgruppen flöde loggning

[NSG-flöde loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) är en funktion i Nätverksbevakaren som du kan använda för att visa information om ingående och utgående IP-trafik via en NSG. Loggarna flödet skrivs i JSON-format och visa:
* Utgående och inkommande flöden på grundval av per regel.
* Nätverkskortet som flödet gäller för.
* 5-tuppel information om flödet: käll- eller mål-IP, källan eller målet porten och protokollet.
* Anger om trafik tillåts eller nekas.

Även om flödet loggar mål NSG: er, visas de inte på samma sätt som andra loggar. Flödet loggfilerna lagras i ett lagringskonto.

Samma bevarandeprinciper som visas på andra loggar gäller flödet loggar. Loggar har en bevarandeprincip som du kan ange från 1 dag till 365 dagar. Om en bevarandeprincip inte är inställd bevaras loggarna för evigt.

**Diagnostikloggar**

Periodiska och eget initiativ händelser skapas av nätverksresurser och inloggad storage-konton och skickas till en händelsehubb eller logganalys. Loggarna ger insikter om hälsotillståndet för en resurs. De kan visas i verktyg som Power BI och logganalys. Information om hur du visar diagnostik loggar finns [logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Diagnostikloggar](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnostik loggar är tillgängliga för [belastningsutjämnaren](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), vägar och [Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Nätverksbevakaren ger en diagnostik loggar vyn. Den här vyn innehåller alla nätverksresurser som har stöd för diagnostik loggning. I den här vyn kan du aktivera och inaktivera nätverksresurser lätt och snabbt.


Utöver de tidigare nämnda loggningsfunktioner har Nätverksbevakaren för närvarande följande funktioner:
- [Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): en på nätverksnivå vy som visar de olika anslutningarna och associationer mellan nätverksresurser i en resursgrupp.

- [Variabeln paketinsamling](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): samlar in paketdata till och från en virtuell dator. Avancerade alternativ för filtrering och justera kontroller, till exempel inställningar för tid - och storleksbegränsning ger flexibilitet. Paketdata kan lagras i en blobstore eller på den lokala disken i *CAP* filformat.

* [IP-flöde verifiering](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): kontrollerar om ett paket tillåts eller nekas baserat på flödet information 5-tuppel paket parametrar (det vill säga mål-IP, käll-IP, målport, källport och protocol). Om paketet nekas av en säkerhetsgrupp, returneras regeln och grupp som nekas paketet.

* [Nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Anger nästa hopp för paket som vidarebefordras i Azure nätverksinfrastruktur, så att du kan diagnostisera eventuella felkonfigurerat användardefinierade vägar.

* [Säkerhet gruppvyn](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): hämtar effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.

* [Virtuella nätverksgatewayen och anslutningen felsökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): hjälper dig att felsöka gateways för virtuella datornätverk och anslutningar.

* [Nätverk prenumerationsbegränsningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits): gör att du kan visa nätverksresursanvändning mot gränser.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar APM-tjänsten för webbutvecklare på flera plattformar. Du kan använda den för att övervaka live webbprogram. Den upptäcker automatiskt prestandaavvikelser. Den inkluderar kraftfulla analysverktyg för att hjälpa dig diagnosticera problem och förstå vad användare faktiskt gör med din app.

Application Insights är utformat för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

Den fungerar för appar på en mängd olika plattformar, inklusive .NET, Node.js och J2EE, oavsett om de finns lokalt eller i molnet. Den kan integreras med DevOps-processen och har kopplingspunkter med olika utvecklingsverktyg.

![Diagram över insikter](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights är avsett för utvecklingsteamet och gör det lättare att förstå hur appen fungerar och hur den används. Tjänsten övervakar:

* **Begära frekvens, svarstider och fel**: ta reda på vilka sidor som är mest populär vid vilka tider på dagen, och var dina användarna finns. Se vilka sidor som fungerar bäst. Om din svarstider och fel går hög när det finns flera begäranden, kanske resourcing problem.

* **Beroende frekvens, svarstider och fel**: ta reda på om externa tjänster saktar ner dig.

* **Undantag**: analysera sammanställda statistik, eller välja specifika instanser och detaljerat stackspårning och relaterade begäranden. Både server- och webbläsarundantag rapporteras.

* **Sidvisningar och Läs in prestanda**: hämta rapporter från användarnas webbläsare.

* **AJAX-anrop**: hämta webbsidan frekvens, svarstider och fel.

* **Antal användare och sessioner**.

* **Prestandaräknare**: hämta data från din Windows- eller Linux-servrar, till exempel processor, minne, och nätverkets användning.

* **Värd för diagnostik**: hämta data från Docker eller Azure.

* **Diagnostik spårningsloggar**: hämta data från din app så att du kan jämföra spårningshändelser där förfrågningar.

* **Anpassade händelser och mått**: hämta data som du kan skriva själv i klient- eller kod för att spåra affärshändelser som försäljning eller vunna.

Följande tabell listar och beskriver integrationsscenarier:

| Integrering med scenario | Beskrivning |
| --------------------- | :---------- |
|[Programavbildningen](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Komponenterna i din app, med viktiga mätvärden och aviseringar.||
|[Diagnostik söka för instans data](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Sök efter och filtrera händelser, till exempel begäranden, undantag, beroendeanrop, loggspårningar och sidvyer.||
|[Metrics Explorer för aggregerade data](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Utforska, filtrera och segmentera aggregerade data, till exempel begärande-, fel- och undantagsfrekvens, svarstider och sidinläsningstider.||
|[Instrumentpaneler](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Kombinera data från flera resurser och dela med andra. Perfekt för program med flera komponenter och för kontinuerlig visning i teamutrymmet.||
|[Direktsänd dataström mått](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|När du distribuerar en ny version kan du titta på dessa prestandaindikatorer som visas i realtid för att kontrollera att allt fungerar som förväntat.||
|[Analys](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Besvara svåra frågor om appens prestanda och användning med hjälp av det här kraftfulla frågespråket.||
|[Automatisk och manuell aviseringar](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatisk aviseringar anpassar sig till din app normal mönster av telemetri och utlöses när det finns något utanför det vanliga mönstret. Du kan också ställa in aviseringar på särskilda nivåer med anpassade mätvärden eller standardmätvärden.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Visa prestandadata i koden. Gå till kod från stackspårningar.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrera användningsmätvärden med annan Business Intelligence.||
|[REST API](https://dev.applicationinsights.io/)|Skriv kod för att köra frågor mot dina mätvärden och rådata.||
|[Löpande export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Massredigera export av rådata till lagring när det kommer fram.||

### <a name="azure-security-center-alerts"></a>Azure Security Center-aviseringar
Hotidentifiering i Azure Security Center fungerar genom att automatiskt samla in säkerhetsinformation från resurserna i Azure, nätverket och anslutna partnerlösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas. Mer information finns i [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagram för Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Det gäller genombrott i stora mängder data och [maskininlärning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tekniker för att utvärdera händelser över hela molninfrastrukturresurs. På så sätt kan upptäcks hot som skulle vara omöjligt att identifiera med hjälp av manuella metoder och förutsäga utvecklingen av attacker. Dessa säkerhetsanalyser omfattar:

* **Integrerad hotinformation**: söker efter kända obehöriga genom att använda globala hotinformation från Microsoftprodukter och tjänster, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa flöden används.

* **Beteendeanalys**: gäller kända mönster för att identifiera skadligt beteende.

* **Avvikelseidentifiering**: använder statistiska profilering för att bygga en historisk baslinje. Den här typen av identifiering varnar vid avvikelser från upprättade baslinjer som matchar vektorn för ett potentiellt angrepp.

Många säkerhetsåtgärder och incidenter team förlitar sig på en SIEM-lösning som startpunkt för triaging och undersöker säkerhetsaviseringar. Du kan synkronisera varningsmeddelanden och virtuella säkerhetshändelser samlas in av Azure diagnostics och granska loggarna med logganalys eller SIEM-lösning i nära realtid med Azure Log-Integration.

## <a name="log-analytics"></a>Log Analytics 

Log Analytics är en tjänst i Azure som hjälper dig att samla in och analysera data som genereras av resurser i molnet och lokala miljöer. Den ger dig realtidsinsikter genom att använda integrerad sökning och anpassade instrumentpaneler för att analysera lätt miljontals poster för alla arbetsbelastningar och servrar, oavsett deras fysiska plats.

![Log Analytics diagram](./media/azure-log-audit/azure-log-audit-fig8.png)

I mitten av de Log Analytics är logganalys-arbetsytan som finns i Azure. Logganalys samlar in data i arbetsytan från anslutna källor genom att konfigurera datakällor och lägga till lösningar till din prenumeration. Datakällor och lösningar för varje skapa olika posttyper, var och en med sin egen uppsättning egenskaper. Men källor och lösningar kan fortfarande analyseras tillsammans i frågor till arbetsytan. Den här funktionen kan du använda samma verktyg och metoder för att arbeta med en mängd olika data som samlas in av olika källor.

Anslutna källor är datorerna och andra resurser som genererar data som samlas in av logganalys. Källor kan innehålla agenter som installerats på [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) och [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) datorer som ansluter direkt eller agenter i [en anslutna System Center Operations Manager-hanteringsgrupp](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Log Analytics kan också samla in data från en [Azure storage-konto](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Datakällor](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) olika typer av data som samlas in från varje anslutna källa. Källor inkludera händelser och [prestandadata](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) från [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) och Linux-agenter, förutom källor som [IIS-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) och [anpassade textloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Du kan konfigurera varje datakälla som du vill samla in och konfigurationen skickas automatiskt till varje ansluten källa.

Det finns fyra olika sätt att [samla in loggar och mått för Azure-tjänster](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):
* Azure Diagnostics direkt till logganalys (**diagnostik** i följande tabell)

* Azure Diagnostics till Azure-lagring till logganalys (**lagring** i följande tabell)

* Kopplingar för Azure-tjänster (**Connector** i följande tabell)

* Skript för att samla in och sedan skicka data till logganalys (tomma celler i tabellen nedan och för tjänster som inte anges)

| Tjänst | Resurstyp | Logs | Mått | Lösning |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostik|Diagnostik|    [Azure-program](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Koppling|  Koppling|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [anslutningsprogrammet (förhandsversion)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Azure Automation-konton| Microsoft.Automation/<br>AutomationAccounts|    Diagnostik||       [Mer information](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Azure Batch-konton|  Microsoft.Batch/<br>batchAccounts|  Diagnostik|    Diagnostik||
|Klassiska molntjänster||       Storage||       [Mer information](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>konton|       Diagnostik|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>konton|   Diagnostik|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>konton|   Diagnostik|||
|Azure Event Hub-namnområde| Microsoft.EventHub/<br>namnområden|  Diagnostik|    Diagnostik||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnostik||
|Azure Key Vault|   Microsoft.KeyVault/<br>valv|  Diagnostik  || [Key Vault-analys](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnostik|||
|Azure Logic Apps|  Microsoft.Logic/<br>Arbetsflöden|  Diagnostik|    Diagnostik||
||Microsoft.Logic/<br>integrationAccounts||||
|Nätverkssäkerhetsgrupper|   Microsoft.Network/<br>networksecuritygroups|Diagnostik||   [Azure Network Security Group analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Recovery-valv|   Microsoft.RecoveryServices/<br>valv|||[Azure Recovery Services Analytics (förhandsgranskning)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Söktjänster|   Microsoft.Search/<br>searchServices|    Diagnostik|    Diagnostik||
|Service Bus-namnområde| Microsoft.ServiceBus/<br>namnområden|    Diagnostik|Diagnostik|    [Service Bus Analytics (förhandsgranskning)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric Analytics (förhandsgranskning)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servrar /<br>databaser||       Diagnostik||
||Microsoft.Sql/<br>servrar /<br>elasticPools||||
|Storage|||         Skript| [Azure Storage Analytics (förhandsgranskning)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft.Compute/<br>virtuella datorer|  Anknytning|  Anknytning||
||||Diagnostik||
|Skalningsuppsättningar för virtuella datorer|    Microsoft.Compute/<br>virtuella datorer    ||Diagnostik||
||Microsoft.Compute/<br>virtualMachineScaleSets /<br>virtuella datorer||||
|Webbservergrupper|Microsoft.Web/<br>serverfarms||   Diagnostik
|Websites|  Microsoft.Web/<br>webbplatser ||      Diagnostik|    [Mer information](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>platser /<br>fack|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Log-integrering med lokala SIEM-system
Med [Azure Log-integrering](https://www.microsoft.com/download/details.aspx?id=53324), du kan integrera loggarna från Azure-resurser med lokala SIEM-system.

![Logga Integration diagram](./media/azure-log-audit/azure-log-audit-fig9.png)

Loggen Integration samlar in Azure-diagnostik från din Windows-datorer, Azure aktivitetsloggar, Azure Security Center-aviseringar och Azure-resurs lagringsproviderns loggfiler. Den här integreringen ger en enhetlig instrumentpanel för alla dina tillgångar, oavsett om de är lokala eller i molnet, så att du kan aggregera korrelera, analysera och varna för säkerhetshändelser.

Log-Integration stöder för närvarande integreringen av Azure aktivitetsloggar, Windows-händelseloggar från Windows-datorer med din Azure-prenumeration, Azure Security Center-aviseringar, Azure diagnostics loggar och Azure AD-granskningsloggar.

| Loggtyp | Log Analytics stöder JSON (Splunk ArcSight och IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Azure AD granskningsloggar|   Ja|
|Aktivitetsloggar| Ja|
|Varningsmeddelanden |Ja|
|Diagnostik-loggar (resurs-loggarna)|  Ja|
|VM-loggar|   Ja, via vidarebefordrade händelser och inte via JSON|

[Kom igång med Azure Log-integrering](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): den här självstudiekursen vägleder dig genom att installera Azure Log-integrering och integrera loggar från Azure storage, Azure aktivitetsloggar Azure Security Center-aviseringar och Azure AD granskningsloggar.

Integrationsscenarier för SIEM:

* [Samarbeta konfigurationssteg](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): det här blogginlägget visar hur du konfigurerar Azure Log-integrering ska fungera med partnerlösningar Splunk, HP ArcSight och IBM QRadar.

* [Azure Log Integration FAQ](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): den här artikeln innehåller svar på frågor om Azure Log-integrering.

* [Integrera Security Center-aviseringar med Azure Log-integrering](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): den här artikeln beskrivs hur du synkroniserar varningsmeddelanden virtuella säkerhetshändelser samlas in av Azure-diagnostik loggar och Azure granskningsloggar med logganalys eller SIEM lösning.

## <a name="next-steps"></a>Nästa steg

- [Granskning och loggning](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): skydda data genom att underhålla synlighet och agera snabbt i rimlig säkerhetsaviseringar.

- [Loggning och granskningsloggen insamling av säkerhet i Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): tillämpa dessa inställningar för att säkerställa att din Azure-instanser att samla in rätt säkerhet och granska loggarna.

- [Konfigurera granskningsinställningar för en webbplatssamling](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Om du är administratör för en webbplatssamling hämta historik över åtgärder för enskilda användare och historik över åtgärder under ett visst datumintervall. 

- [Söka granskningsloggen i Office 365 säkerhets- och Efterlevnadscentret](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): använda Office 365 säkerhets- och Efterlevnadscentret för att söka enhetlig granskningsloggen och visa aktiviteten för användare och administratör i din Office 365-organisation.


