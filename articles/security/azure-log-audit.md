---
title: Azure-loggning och granskning | Microsoft Docs
description: Lär dig mer om hur du kan använda loggnings data för att få djupgående insikter om ditt program.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: 6634b674037b48ff9ef9690144ef5274f31a6ea8
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227967"
---
# <a name="azure-logging-and-auditing"></a>Loggning och granskning i Azure

Azure tillhandahåller en mängd olika konfigurerbara säkerhets gransknings-och loggnings alternativ som hjälper dig att identifiera luckor i dina säkerhets principer och-mekanismer. Den här artikeln beskriver hur du skapar, samlar in och analyserar säkerhets loggar från tjänster som finns i Azure.

> [!Note]
> Vissa rekommendationer i den här artikeln kan leda till ökad data-, nätverks-eller beräknings resursanvändning och öka dina licens-eller prenumerations kostnader.

## <a name="types-of-logs-in-azure"></a>Typer av loggar i Azure

Moln program är komplexa, med många rörliga delar. Loggar innehåller data som hjälper dig att hålla programmen igång. Loggarna hjälper dig att felsöka tidigare problem eller förhindra att de är möjliga. De kan hjälpa till att förbättra programmets prestanda eller hanterbarhet eller automatisera åtgärder som annars skulle kräva manuell åtgärd.

Azure-loggar kategoriseras i följande typer:
* **Kontroll-/hanterings loggar** innehåller information om Azure Resource Manager Skapa, uppdatera och ta bort åtgärder. Mer information finns i [Azure aktivitets loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Data Plans loggar** innehåller information om händelser som aktive ras som en del Azure-resursanvändning. Exempel på den här typen av logg är Windows händelse system-, säkerhets-och program loggar i en virtuell dator (VM) och [de diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) som konfigureras via Azure Monitor.

* **Bearbetade händelser** ger information om analyserade händelser/aviseringar som har bearbetats för din räkning. Exempel på den här typen är [Azure Security Center varningar](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) där [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) har bearbetat och analyserat din prenumeration och ger kortfattade säkerhets aviseringar.

I följande tabell visas de viktigaste typerna av loggar som är tillgängliga i Azure:

| Loggkategori | Loggtyp | Användning | Integrering |
| ------------ | -------- | ------ | ----------- |
|[Aktivitets loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Kontroll – plan händelser på Azure Resource Manager resurser|   Ger insikt i de åtgärder som utfördes på resurser i din prenumeration.|    REST-API, [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure Diagnostics-loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Frekventa data om driften av Azure Resource Manager resurser i prenumerationen|    Ger inblick i åtgärder som din resurs själv utfört.| Azure Monitor, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Azure AD-rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Loggar och rapporter | Rapporterar användar inloggnings aktiviteter och system aktivitets information om användare och grupp hantering.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuella datorer och moln tjänster](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Windows händelse logg tjänst och Linux syslog|  Avbildar system data och loggnings data på de virtuella datorerna och överför dessa data till ett valfritt lagrings konto.|   Windows (med Windows Azure-diagnostik [[wad](https://docs.microsoft.com/azure/azure-diagnostics)] Storage) och Linux i Azure Monitor|
|[Azure-lagringsanalys](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Lagrings loggning, tillhandahåller mått data för ett lagrings konto|Ger insyn i trace-begäranden, analyserar användnings trender och diagnostiserar problem med ditt lagrings konto.|   REST API eller [klient biblioteket](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Flödes loggar för nätverks säkerhets gruppen (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON-format, visar utgående och inkommande flöden per regel|Visar information om inkommande och utgående IP-trafik via en nätverks säkerhets grupp.|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Program insikter](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Loggar, undantag och anpassad diagnostik|   Tillhandahåller en tjänst för program prestanda övervakning (APM) för webbutvecklare på flera plattformar.| REST API [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Process data/säkerhets aviseringar|    Azure Security Center aviseringar Azure Monitor loggar aviseringar|    Tillhandahåller säkerhets information och aviseringar.|  REST API: er, JSON|

### <a name="activity-logs"></a>Aktivitetsloggar

[Azure aktivitets loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger insikt i de åtgärder som utfördes på resurser i din prenumeration. Aktivitets loggar kallades tidigare för "gransknings loggar" eller "drift loggar", eftersom de rapporterar [kontroll Plans händelser](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) för dina prenumerationer. 

Aktivitets loggarna hjälper dig att fastställa "vad, vem och när" för Skriv åtgärder (det vill säga, publicera eller ta bort). Aktivitets loggar hjälper dig också att förstå status för åtgärden och andra relevanta egenskaper. Aktivitets loggar innehåller inte Läs åtgärder (GET).

I den här artikeln får du lägga till, posta och ta bort information om alla Skriv åtgärder som en aktivitets logg innehåller på resurserna. Du kan till exempel använda aktivitets loggarna för att hitta ett fel när du felsöker problem eller för att övervaka hur en användare i organisationen har ändrat en resurs.

![Aktivitets logg diagram](./media/azure-log-audit/azure-log-audit-fig1.png)

Du kan hämta händelser från en aktivitets logg med hjälp av Azure Portal, [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), PowerShell-cmdletar och [Azure Monitor REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Aktivitets loggarna har 90 dagars data – kvarhållningsperiod.

Integrations scenarier för en aktivitets logg händelse:

* [Skapa en e-postadress eller webhook-avisering som utlöses av en aktivitets logg händelse](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Strömma det till en Event Hub](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) för inmatning av en tjänst från tredje part eller en anpassad analys lösning som PowerBI.

* Analysera den i PowerBI med hjälp av [PowerBI-innehålls paketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Spara det till ett lagrings konto för arkivering eller manuell kontroll](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Du kan ange Retentions tid (i dagar) med hjälp av logg profiler.

* Fråga och visa den i Azure Portal.

* Fråga den via PowerShell-cmdlet, Azure CLI eller REST API.

* Exportera aktivitets loggen med logg profiler till [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Du kan använda ett lagrings konto eller ett [namn område för händelsehubben](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) som inte finns i samma prenumeration som den som avger loggen. Användare som konfigurerar inställningen måste ha lämplig [rollbaserad åtkomst kontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) för båda prenumerationerna.

### <a name="azure-diagnostics-logs"></a>Azure Diagnostics-loggar

Azure Diagnostics-loggar genereras av en resurs som ger omfattande, frekventa data om driften av resursen. Innehållet i de här loggarna varierar efter resurstyp. [Windows Event System-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) är till exempel en kategori av diagnostikloggar för virtuella datorer, och [BLOB-, tabell-och Queue-](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) loggar är kategorier av diagnostikloggar för lagrings konton. Diagnostikloggar skiljer sig från aktivitets loggar, som ger inblick i de åtgärder som utfördes på resurser i din prenumeration.

![Diagram över Azure Diagnostics-loggar](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure Diagnostics-loggar erbjuder flera konfigurations alternativ, till exempel Azure Portal, PowerShell, Azure CLI och REST API.

**Integrations scenarier**

* Spara dem till ett [lagrings konto](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) för granskning eller manuell kontroll. Du kan ange Retentions tid (i dagar) med hjälp av diagnostikinställningar.

* [Strömma dem till händelse hubbar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) för inmatning av en tjänst från tredje part eller en anpassad analys lösning, till exempel [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analysera dem med [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Tjänster som stöds, schema för diagnostikloggar och logg kategorier som stöds per resurs typ**


| Tjänsten | Schema och dokumentation | Resurstyp | Category |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Azure Monitor loggar för Load Balancer (förhands granskning)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Nätverkssäkerhetsgrupper|[Azure Monitor loggar för nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Diagnostikloggning för Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Key Vault loggar](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Aktivera och använda Sök Trafikanalys](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Åtkomst till diagnostikloggar för Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Granska<br>Begäranden|
|Azure Data Lake Analytics|[Åtkomst till diagnostikloggar för Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Granska<br>Begäranden|
|Azure Logic Apps|[Anpassat Logic Apps B2B-spårningsschema](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch diagnostikloggar](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Azure Monitor loggar för Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft. Automation/automationAccounts<br>Microsoft. Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Event Hubs diagnostikloggar](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Jobb diagnostikloggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Körning<br>Redigering|
|Azure Service Bus|[Service Bus-diagnostikloggar](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory-rapportering

Azure Active Directory (Azure AD) innehåller rapporter om säkerhet, aktivitet och granskning för en användares katalog. Med [gransknings rapporten i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) kan du identifiera privilegierade åtgärder som har inträffat i användarens Azure AD-instans. Privilegierade åtgärder är ändringar i förhöjd behörighet (till exempel skapande av roller eller återställning av lösen ord), ändring av princip konfiguration (till exempel lösen ords principer) eller ändringar i katalog konfigurationen (till exempel ändringar i domän Federations inställningar).

Rapporterna innehåller gransknings posten för händelse namnet, den användare som utförde åtgärden, mål resursen som påverkas av ändringen och datum och tid (i UTC). Användare kan hämta listan över gransknings händelser för Azure AD via [Azure Portal](https://portal.azure.com/), enligt beskrivningen i [Visa gransknings loggarna](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

De inkluderade rapporterna visas i följande tabell:

| Säkerhetsrapporter | Aktivitetsrapporter | Granskningsrapporter |
| :--------------- | :--------------- | :------------ |
|Inloggningar från okända källor| Programanvändning: sammanfattning| Kataloggranskningsrapport|
|Inloggningar efter flera fel|  Programanvändning: detaljerad||
|Inloggningar från flera geografiska områden|    Instrumentpanel för program||
|Inloggningar från IP-adresser med misstänkt aktivitet|   Kontoetableringsfel||
|Oregelbunden inloggningsaktivitet|    Enskilda användarenheter||
|Inloggningar från potentiellt infekterade enheter|   Enskild användar aktivitet||
|Användare med avvikande inloggningsaktivitet| Aktivitetsrapport för grupper||
||Registrera aktivitets rapport för lösen ords återställning||
||Lösenordsåterställningsaktivitet||

Data i dessa rapporter kan vara användbara för dina program, till exempel SIEM-system, gransknings-och Business Intelligence verktyg. Azure AD reporting API: er ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa dessa [API: er](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) från olika programmeringsspråk och verktyg.

Händelse kvarhållning i gransknings rapporten i Azure AD varierar mellan 7-90 dagar beroende på vilken typ av licens som är associerad med klienten. 

> [!Note]
> Mer information om kvarhållning av rapporter finns i [Azure AD Report kvarhållning-principer](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Om du vill behålla gransknings händelserna längre använder du rapporterings-API: t för att regelbundet hämta [gransknings händelser](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) till ett separat data lager.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Virtuella dator loggar som använder Azure-diagnostik

[Azure-diagnostik](https://docs.microsoft.com/azure/azure-diagnostics) är funktionen i Azure som möjliggör insamling av diagnostikdata i ett distribuerat program. Du kan använda diagnostik-tillägget från någon av flera källor. För närvarande finns stöd för [webb-och arbets roller för Azure Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Virtuella dator loggar som använder Azure-diagnostik](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Virtuella Azure-datorer](/learn/paths/deploy-a-website-with-azure-virtual-machines/) som kör Microsoft Windows och [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Du kan aktivera Azure-diagnostik på en virtuell dator genom att göra något av följande:

* [Använd Visual Studio för att spåra virtuella Azure-datorer](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Konfigurera Azure-diagnostik fjärr anslutning på en virtuell Azure-dator](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Använd PowerShell för att konfigurera diagnostik på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Skapa en virtuell Windows-dator med övervakning och diagnostik med hjälp av en Azure Resource Manager mall](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Lagringsanalys

[Azure-lagringsanalys](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) loggar och tillhandahåller mått data för ett lagrings konto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot. Lagringsanalys loggning är tillgängligt för [Azure-blobben, Azure Queue och Azure Table Storage-tjänster](https://docs.microsoft.com/azure/storage/storage-introduction). Lagringsanalys loggar detaljerad information om lyckade och misslyckade förfrågningar till en lagrings tjänst.

Du kan använda den här informationen för att övervaka enskilda förfrågningar och diagnostisera problem med en lagrings tjänst. Begär Anden loggas med bästa möjliga ansträngning. Logg poster skapas endast om det finns begär Anden som görs mot tjänst slut punkten. Om ett lagrings konto till exempel har aktivitet i dess BLOB-slutpunkt men inte i dess tabell-eller Queue-slutpunkter, skapas bara loggar som hör till Blob Storage-tjänsten.

Om du vill använda Lagringsanalys aktiverar du det separat för varje tjänst som du vill övervaka. Du kan aktivera det i [Azure Portal](https://portal.azure.com/). Mer information finns i [övervaka ett lagrings konto i Azure Portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Du kan också aktivera Lagringsanalys program mässigt via REST API eller klient biblioteket. Använd åtgärden Ange tjänst egenskaper för att aktivera Lagringsanalys individuellt för varje tjänst.

De aggregerade data lagras i en välkänd BLOB (för loggning) och i välkända tabeller (för mått) som du kan komma åt med hjälp av API: er för Blob Storage-tjänsten och table Storage-tjänsten.

Lagringsanalys har en gräns på 20 terabyte (TB) för mängden lagrade data som är oberoende av den totala gränsen för ditt lagrings konto. Alla loggar lagras i [block](https://docs.microsoft.com/azure/storage/storage-analytics) -blobar i en behållare med namnet $logs, som skapas automatiskt när du aktiverar Lagringsanalys för ett lagrings konto.

> [!Note]
> * Mer information om principer för fakturering och data lagring finns i [Lagringsanalys och fakturering](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Mer information om begränsningar för lagrings konton finns i [Azure Storage skalbarhets-och prestanda mål](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Lagringsanalys loggar följande typer av autentiserade och anonyma begär Anden:

| Autentiserad  | Anonym|
| :------------- | :-------------|
| Lyckade förfrågningar | Lyckade förfrågningar |
|Misslyckade förfrågningar, inklusive timeout, begränsning, nätverk, auktorisering och andra fel | Begär Anden som använder signaturer för delad åtkomst, inklusive misslyckade och lyckade förfrågningar |
| Begär Anden som använder signaturer för delad åtkomst, inklusive misslyckade och lyckade förfrågningar |Timeout-fel för både klienten och servern |
|   Begär Anden för analys av data |    Misslyckade GET-begäranden med felkod 304 (inte ändrad) |
| Begär Anden som görs av Lagringsanalys, till exempel när loggen skapas eller tas bort loggas inte. En fullständig lista över loggade data dokumenteras i [Lagringsanalys loggade åtgärder och status meddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys logg format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Alla andra misslyckade anonyma begär Anden loggas inte. En fullständig lista över loggade data dokumenteras i [Lagringsanalys loggade åtgärder och status meddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys logg format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Azure nätverks loggar

Nätverks loggning och övervakning i Azure är omfattande och omfattar två breda kategorier:

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Scenario-baserad nätverks övervakning tillhandahålls med funktionerna i Network Watcher. Den här tjänsten omfattar paket insamling, nästa hopp, kontrol lera IP-flöde, vyn säkerhets grupp, NSG Flow-loggar. Övervakning av scenario nivå ger en heltäckande vy över nätverks resurser i motsats till övervakning av enskilda nätverks resurser.

* [Resurs övervakning](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Övervakning av resurs nivå omfattar fyra funktioner, diagnostikloggar, statistik, fel sökning och resurs hälsa. Alla dessa funktioner bygger på nätverks resurs nivån.

![Azure nätverks loggar](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks scenario nivå i, till och från Azure. Nätverksdiagnostik och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure.

### <a name="network-security-group-flow-logging"></a>Flödes loggning för nätverks säkerhets grupp

[NSG flödes loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) är en funktion i Network Watcher som du kan använda för att visa information om inkommande och utgående IP-trafik via en NSG. Dessa flödes loggar skrivs i JSON-format och visas:
* Utgående och inkommande flöden per regel.
* Det nätverkskort som flödet gäller för.
* 5-tuple-information om flödet: källans eller målets IP-adress, käll-eller mål Port och protokoll.
* Om trafiken tillåts eller nekades.

Även om Flow loggar mål NSG: er, visas de inte på samma sätt som de andra loggarna. Flödes loggar lagras bara inom ett lagrings konto.

Samma bevarande principer som visas i andra loggar gäller för flödes loggar. Loggar har en bevarande princip som du kan ställa in från 1 dag till 365 dagar. Om en bevarandeprincip inte är inställd bevaras loggarna för evigt.

**Diagnostikloggar**

Periodiska och spontana händelser skapas av nätverks resurser och loggas på lagrings konton och skickas till en Event Hub-eller Azure Monitor-loggar. Loggarna ger insikter om hälso tillståndet för en resurs. De kan visas i verktyg som Power BI och Azure Monitor loggar. Information om hur du visar diagnostikloggar finns i [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Diagnostikloggar](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnostikloggar är tillgängliga för [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), vägar och [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher innehåller en vy för diagnostikloggar. Den här vyn innehåller alla nätverks resurser som stöder diagnostikloggning. I den här vyn kan du aktivera och inaktivera nätverks resurser enkelt och snabbt.


Förutom de tidigare nämnda loggnings funktionerna har Network Watcher för närvarande följande funktioner:
- [Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Ger en vy på nätverks nivå som visar de olika samman kopplingarna och associationerna mellan nätverks resurser i en resurs grupp.

- [Inspelning av variabel paket](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Fångar paket data in och ut ur en virtuell dator. Avancerade filtrerings alternativ och fin justering av kontroller, till exempel inställningar för tids-och storleks begränsning, ger mångsidighet. Paket data kan lagras i ett BLOB-lager eller på den lokala disken i *. Cap* -filformat.

- [Verifiera IP-flöde](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Kontrollerar om ett paket tillåts eller nekas baserat på flödes information 5-tuple paket parametrar (det vill säga målets IP-adress, käll-IP, målport, källport och protokoll). Om paketet nekas av en säkerhets grupp returneras regeln och gruppen som nekade paketet.

- [Nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Anger nästa hopp för paket som dirigeras i Azures nätverks infrastruktur resurs, så att du kan diagnostisera eventuella felkonfigurerade användardefinierade vägar.

- [Vy över säkerhets grupp](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Hämtar de effektiva och tillämpade säkerhets reglerna som tillämpas på en virtuell dator.

- [Virtuell nätverksgateway och anslutnings fel sökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Hjälper dig att felsöka virtuella nätverks-gatewayer och anslutningar.

- [Begränsningar för nätverks prenumeration](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Gör att du kan visa användning av nätverks resurser mot gränser.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utöknings bar APM-tjänst för webbutvecklare på flera plattformar. Använd den för att övervaka Live-webbprogram. Prestanda avvikelser identifieras automatiskt. Den inkluderar kraftfulla analysverktyg för att hjälpa dig diagnosticera problem och förstå vad användare faktiskt gör med din app.

Application Insights har utformats för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

Det fungerar för appar på en mängd olika plattformar, inklusive .NET, Node. js och Java-EE, oavsett om de finns lokalt eller i molnet. Den integreras med din DevOps-process och har anslutnings punkter med olika utvecklingsverktyg.

![Application Insights diagram](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights är avsett för utvecklingsteamet och gör det lättare att förstå hur appen fungerar och hur den används. Tjänsten övervakar:

* **Begär ande frekvens, svars tider och felaktiga frekvenser**: Ta reda på vilka sidor som är mest populära, vid vilka tidpunkter på dagen och var dina användare är. Se vilka sidor som fungerar bäst. Om svars tiderna och fel frekvensen går högt när det finns fler begär Anden kan du ha ett problem med en omkällning.

* **Beroende frekvens, svars tider och felaktiga frekvenser**: Ta reda på om de externa tjänsterna saktar ned dig.

* **Undantag**: Analysera den sammanställda statistiken eller Välj vissa instanser och öka detalj nivån i stack spårningen och relaterade begär Anden. Både server- och webbläsarundantag rapporteras.

* **Sid visningar och belastnings prestanda**: Hämta rapporter från användarnas webbläsare.

* **AJAX-anrop**: Hämta frekvenser för webb sidor, svars tider och felaktiga frekvenser.

* **Antal användare och sessioner**.

* **Prestanda räknare**: Hämta data från dina Windows-eller Linux Server-datorer, till exempel processor, minne och nätverks användning.

* **Host Diagnostics**: Hämta data från Docker eller Azure.

* **Spårnings loggar för diagnostik**: Hämta data från din app så att du kan korrelera spårnings händelser med begär Anden.

* **Anpassade händelser och mått**: Hämta data som du skriver själv i klient-eller Server koden för att spåra affärs händelser som sålda objekt eller spel som har vunnits.

I följande tabell visas och beskrivs integrerings scenarier:

| Integrations scenario | Beskrivning |
| --------------------- | :---------- |
|[Program karta](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Komponenterna i din app, med viktiga mätvärden och aviseringar.|
|[Diagnostik-Sök efter instans data](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Sök efter och filtrera händelser, till exempel begäranden, undantag, beroendeanrop, loggspårningar och sidvyer.|
|[Metrics Explorer för sammanställda data](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|Utforska, filtrera och segmentera aggregerade data, till exempel begärande-, fel- och undantagsfrekvens, svarstider och sidinläsningstider.|
|[Instrumentpaneler](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)|Kombinera data från flera resurser och dela med andra. Perfekt för program med flera komponenter och för kontinuerlig visning i teamutrymmet.|
|[Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|När du distribuerar en ny version kan du titta på dessa prestandaindikatorer som visas i realtid för att kontrollera att allt fungerar som förväntat.|
|[Analys](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Besvara svåra frågor om appens prestanda och användning med hjälp av det här kraftfulla frågespråket.|
|[Automatiska och manuella aviseringar](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatiska aviseringar anpassas till appens normala mönster för telemetri och utlöses när det finns något utanför det vanliga mönstret. Du kan också ställa in aviseringar på särskilda nivåer med anpassade mätvärden eller standardmätvärden.|
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Visa prestanda data i koden. Gå till kod från stackspårningar.|
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrera användningsmätvärden med annan Business Intelligence.|
|[REST-API](https://dev.applicationinsights.io/)|Skriv kod för att köra frågor mot dina mätvärden och rådata.|
|[Löpande export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Mass export av rå data till lagring när den tas emot.|

### <a name="azure-security-center-alerts"></a>Azure Security Center aviseringar

Azure Security Center hot identifieringen fungerar genom att automatiskt samla in säkerhets information från dina Azure-resurser, nätverket och anslutna partner lösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas. Mer information finns i [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Azure Security Center diagram](./media/azure-log-audit/azure-log-audit-fig7.png)

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Den använder genombrott i stora data-och [maskin inlärnings](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tekniker för att utvärdera händelser i hela molnets infrastruktur resurser. På så sätt upptäcker den hot som skulle vara omöjlig att identifiera genom att använda manuella metoder och förutsäga utvecklingen av attacker. Dessa säkerhetsanalyser omfattar:

* **Integrerad Hot information**: Söker efter kända dåliga aktörer genom att använda global Hot information från Microsofts produkter och tjänster, Microsoft Digital brottslighet-enheten (DCU), Microsoft Security Response Center (MSRC) och externa feeds.

* **Beteende analys**: Använder kända mönster för att identifiera skadligt beteende.

* **Avvikelse identifiering**: Använder statistisk profilering för att bygga en historisk bas linje. Den här typen av identifiering varnar vid avvikelser från upprättade baslinjer som matchar vektorn för ett potentiellt angrepp.

Många säkerhets åtgärder och svars grupper för incidenter förlitar sig på en SIEM-lösning som utgångs punkt för sorterar och undersöker säkerhets aviseringar. Med Azure Log Integration kan du synkronisera Security Center aviseringar och säkerhets händelser för virtuella datorer som samlas in av Azure Diagnostics och gransknings loggar, med dina Azure Monitor loggar eller SIEM-lösning i nära real tid.

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Azure Monitor loggar är en tjänst i Azure som hjälper dig att samla in och analysera data som genereras av resurser i molnet och i lokala miljöer. Det ger dig real tids insikter genom att använda integrerad sökning och anpassade instrument paneler för att enkelt analysera miljon tals poster över alla dina arbets belastningar och servrar, oavsett deras fysiska plats.

![Diagram över Azure Monitors loggar](./media/azure-log-audit/azure-log-audit-fig8.png)

I mitten av Azure Monitor loggar är arbets ytan Log Analytics, som finns i Azure. Azure Monitor loggar samlar in data i arbets ytan från anslutna källor genom att konfigurera data källor och lägga till lösningar i din prenumeration. Data källor och lösningar varje skapar olika post typer, var och en med en egen uppsättning egenskaper. Men källor och lösningar kan fortfarande analyseras tillsammans i frågor till arbets ytan. Med den här funktionen kan du använda samma verktyg och metoder för att arbeta med en mängd olika data som samlas in av en rad olika källor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Anslutna källor är datorer och andra resurser som genererar data som samlas in av Azure Monitor loggar. Källor kan omfatta agenter som är installerade på [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) -och [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) -datorer som ansluter direkt eller agenter i [en ansluten System Center Operations Manager hanterings grupp](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Azure Monitor loggar kan även samla in data från ett [Azure Storage-konto](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Data källor](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) är de olika typerna av data som samlas in från varje ansluten källa. Källor innehåller händelser och [prestanda data](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) från [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) -och Linux-agenter, förutom källor som [IIS-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) och [anpassade text loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Du kan konfigurera varje datakälla som du vill samla in och konfigurationen skickas automatiskt till varje ansluten källa.

Det finns fyra sätt att [samla in loggar och mät värden för Azure-tjänster](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):

* Azure-diagnostik direkt till Azure Monitor loggar (**diagnostik** i följande tabell)

* Azure-diagnostik till Azure Storage för att Azure Monitor loggar (**lagring** i följande tabell)

* Anslutningar för Azure-tjänster (**koppling** i följande tabell)

* Skript för att samla in och sedan skicka data till Azure Monitor loggar (tomma celler i följande tabell och för tjänster som inte visas)

| Tjänsten | Resurstyp | Logs | Mått | Lösning |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostik|Diagnostik|    [Azure Application](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics) [Gateway-analys](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     Koppling|  Koppling|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Koppling (för hands version)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Azure Automation konton| Microsoft.Automation/<br>AutomationAccounts|    Diagnostik||       [Mer information](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Azure Batch konton|  Microsoft.Batch/<br>batchAccounts|  Diagnostik|    Diagnostik||
|Klassiska molntjänster||       Storage||       [Mer information](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>konton|       Diagnostik|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>konton|   Diagnostik|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>konton|   Diagnostik|||
|Azure Event Hub-namnrymd| Microsoft.EventHub/<br>namn områden|  Diagnostik|    Diagnostik||
|Azure IoT Hub| Microsoft. Devices/<br>IotHubs||     Diagnostik||
|Azure Key Vault|   Microsoft.KeyVault/<br>valv|  Diagnostik  || [Key Vault-analys](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>Belastningsutjämnare|    Diagnostik|||
|Azure Logic Apps|  Microsoft. Logic/<br>arbetsflöden|  Diagnostik|    Diagnostik||
||Microsoft. Logic/<br>integrationAccounts||||
|Nätverkssäkerhetsgrupper|   Microsoft.Network/<br>networksecuritygroups|Diagnostik||   [Analys av Azure nätverks säkerhets grupp](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-and-network-security-group-analytics)|
|Recovery-valv|   Microsoft.RecoveryServices/<br>valv|||[Azure Recovery Services-analys (förhandsversion)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Söktjänster|   Microsoft.Search/<br>searchServices|    Diagnostik|    Diagnostik||
|Service Bus-namnområde| Microsoft.ServiceBus/<br>namn områden|    Diagnostik|Diagnostik|    [Service Bus-analys (förhandsversion)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric-analys (förhandsversion)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>brygghuvudservrar<br>databases||       Diagnostik||
||Microsoft.Sql/<br>brygghuvudservrar<br>elasticPools||||
|Storage|||         Skript| [Azure Storage-analys (förhandsversion)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft. Compute/<br>virtualMachines|  Anknytning|  Anknytning||
||||Diagnostik||
|Skalningsuppsättningar för virtuella datorer|    Microsoft. Compute/<br>virtualMachines    ||Diagnostik||
||Microsoft. Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Webb Server grupper|Microsoft.Web/<br>Server grupper||   Diagnostik
|Websites|  Microsoft.Web/<br>stationer ||      Diagnostik|    [Mer information](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>stationer<br>lots||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Logg integrering med lokala SIEM-system

Med Azure Log Integration kan du integrera obehandlade loggar från dina Azure-resurser med ditt lokala SIEM-system (säkerhets information och händelse hanterings system). AzLog hämtningar inaktiverades den 27 juni 2018. Information om vad du kan göra när du flyttar framåt finns i [använda Azure Monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Logg integrerings diagram](./media/azure-log-audit/azure-log-audit-fig9.png)

Logg integrering samlar in Azure Diagnostics från dina virtuella Windows-datorer, Azure-aktivitets loggar, Azure Security Center aviseringar och Azure Resource Provider-loggar. Den här integrationen tillhandahåller en enhetlig instrument panel för alla dina till gångar, oavsett om de är lokala eller i molnet, så att du kan samla in, korrelera, analysera och varna för säkerhets händelser.

Log integration stöder för närvarande integrering av Azure-aktivitets loggar, Windows-händelseloggar från virtuella Windows-datorer med din Azure-prenumeration, Azure Security Center aviseringar, Azure-diagnostikloggar och Azure AD audit-loggar.

| Loggtyp | Azure Monitor loggar som stöder JSON (Splunk, ArcSight och IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Gransknings loggar för Azure AD|   Ja|
|Aktivitetsloggar| Ja|
|Security Center aviseringar |Ja|
|Diagnostikloggar (resurs loggar)|  Ja|
|VM-loggar|   Ja, via vidarebefordrade händelser och inte via JSON|

[Kom igång med Azure log integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Den här självstudien vägleder dig genom installationen av Azure Log Integration och integrering av loggar från Azure Storage, Azure aktivitets loggar, Azure Security Center aviseringar och Azure AD audit-loggar.

Integrerings scenarier för SIEM:

* [Konfigurations steg för partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Det här blogg inlägget visar hur du konfigurerar Azure Log Integration att arbeta med partner Solutions Splunk, HP ArcSight och IBM QRadar.

* [Azure log integration vanliga frågor och svar](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Den här artikeln ger svar på frågor om Azure Log Integration.

* [Integrera Security Center aviseringar med Azure log integration](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Den här artikeln beskriver hur du synkroniserar Security Center aviseringar, säkerhets händelser för virtuella datorer som samlats in av Azure Diagnostics-loggar och Azure audit-loggar med Azure Monitor loggar eller SIEM-lösning.

## <a name="next-steps"></a>Nästa steg

- [Granskning och loggning](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): Skydda data genom att bevara synlighet och svara snabbt på säkerhets aviseringar.

- [Säkerhets loggning och granskning – logg insamling i Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Använd de här inställningarna för att se till att dina Azure-instanser samlar in rätt säkerhets-och gransknings loggar.

- [Konfigurera gransknings inställningar för en webbplats samling](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Om du är administratör för webbplats samling hämtar du historiken för enskilda användares åtgärder och historiken för de åtgärder som vidtagits under ett visst datum intervall. 

- [Sök i gransknings loggen i Office 365-säkerhets-och Efterlevnadscenter](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Använd Office 365-Säkerhets-och efterlevnadscenter för att söka i enhetlig Gransknings logg och Visa användar-och administratörs aktivitet i din Office 365-organisation.


