---
title: Azure-loggning och granskning | Microsoft Docs
description: Läs mer om hur du kan använda loggning av data och få djupa insikter om ditt program.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 032aa4a6cedd49ff9c3b4803561b8b187e8f9af5
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="azure-logging-and-auditing"></a>Azure-loggning och granskning
## <a name="introduction"></a>Introduktion
### <a name="overview"></a>Översikt
Microsoft har utvecklat en serie faktablad, säkerhet översikter, bästa praxis och checklistor som hjälper aktuella och framtida Azure-kunder för att förstå och använda olika säkerhetsrelaterade funktioner tillgängliga i och omgivande Azure-plattformen. Avsnitten intervallet vad gäller bredd och djup och uppdateras regelbundet. Det här dokumentet är en del av serien som sammanfattas i följande avsnitt i abstrakt.
### <a name="azure-platform"></a>Azure Platform
Azure är en öppen och flexibla molntjänstplattform som stöder bredaste valet av operativsystem, programmeringsspråk, ramverk, verktyg, databaser, och enheter.

Du kan till exempel:
-   Kör Linux behållare med Docker-integration.

-   Skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js

-   Build-servrar för iOS, Android och Windows enheter.

Offentliga Azure-molnet och support att samma tekniker miljontals utvecklare och IT-proffs redan förlitar sig på och litar på.

När du bygger på, eller migrera IT tillgångar, en molntjänstleverantör, måste den organisationens förmåga att skydda dina program och data med tjänster och de kontroller som de tillhandahåller för att hantera säkerheten för din molnbaserade tillgångar.

Allt i Azures infrastruktur, från anläggning till tillämpningar, är utformat för att fungera som värd för miljoner kunder samtidigt, och den tillhandahåller en säker grund som företaget kan använda sig av för att möta de interna säkerhetsbehoven. Dessutom erbjuder Azure dig en mängd olika konfigurerbara säkerhetsalternativ samt möjligheten att kontrollera dem, så att du kan anpassa säkerheten för att uppfylla de specifika behoven hos dina distributioner. Det här dokumentet kommer hjälper dig att möta dessa krav.

### <a name="abstract"></a>Abstrakt
Granskning och loggning av säkerhetsrelaterade händelser och relaterade aviseringar är viktiga komponenter i en strategi för skydd av effektiva data. Säkerhetsloggar och rapporterna ger dig en elektronisk post av misstänkta aktiviteter och hjälp dig att upptäcka mönster som kan indikera försök eller lyckad externa intrång av nätverk, samt interna attacker. Du kan använda granskning för att övervaka användaraktivitet, dokumentet regelefterlevnad, utföra kriminaltekniska analyser och mycket mer. Omedelbar avisering med aviseringar när säkerhetshändelser inträffar.

Microsoft Azure-tjänster och produkter som ger dig konfigurerbara säkerhet granskning och loggning alternativ som hjälper dig att identifiera brister i säkerhetsprinciper och mekanismer och åtgärda dessa luckor om du vill undvika överträdelser. Microsoft-tjänster erbjuder vissa (och i vissa fall alla) av följande alternativ: centraliserad övervakning, loggning och analyssystem som ger kontinuerlig synlighet; rimlig aviseringar; och rapporter som hjälper dig att hantera den stora mängden information som genereras av enheter och tjänster.

Microsoft Azure-loggdata kan exporteras till säkerhetsincident och händelsen Management SIEM ()-system för analys och integreras med granskning tredjepartslösningar.

Den här rapporten innehåller en introduktion för generering, samla in och analysera säkerhetsloggar från i Azure-tjänster och kan hjälpa dig få säkerhetsinsikter om dina Azure-distributioner. Omfånget för det här dokumentet är begränsat till program och tjänster inbyggd och distribuerade i Azure.

> [!Note]
> Vissa rekommendationerna häri kan leda till ökad data, nätverk eller beräkning Resursanvändning och öka kostnaderna licens eller prenumeration.

## <a name="types-of-logs-in-azure"></a>Typer av loggar i Azure
Molnprogram är komplicerade med många rörliga delar. Loggarna ger data så att programmet håller sig in och körs i ett felfritt tillstånd. Det hjälper dig också att stave ut potentiella problem eller felsöka tidigare viktiga. Dessutom kan du använda loggning av data och få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda eller underhålla eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

Azure ger utförlig loggning för varje Azure-tjänst. Dessa loggar kategoriseras av dessa typer:
-   **Kontroll och hantering loggar** ger inblick i Azure Resource Manager skapa-, UPDATE- och DELETE-åtgärder. [Azure aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) är ett exempel på den här typen av loggen.

-   **Data plan loggar** ger inblick i händelser som aktiverats som en del av användningen av en Azure-resurs. Exempel på den här typen av loggen är Windows-händelse System, säkerhet, och program som loggar in på en virtuell dator och [diagnostik loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) konfigureras via Azure-Monitor


-   **Bearbetade händelser** ge information om analyserade/aviseringar som har bearbetats å dina vägnar. Exempel på den här typen är [Azure Security Center-aviseringar](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) där [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) har bearbetats analyseras och din prenumeration och ger kortare säkerhetsaviseringar

I följande tabell listas de viktigaste typ av loggar som är tillgängliga i Azure.

| Loggen kategori | Loggtyp | Användningsområden | Integrering |
| ------------ | -------- | ------ | ----------- |
|[Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Kontroll-plan händelser på Azure Resource Manager-resurser|   Ger information om åtgärder som utfördes på resurser i din prenumeration.| REST-API: T & [Azure Övervakare](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|ofta data om handhavandet av Azure Resource Manager resurserna i prenumerationen| Ger information om åtgärder som din resurs utförde själva| Övervakare för Azure [dataström](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[AAD-rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Loggar och rapporter|Logga in användaraktiviteter & System aktivitetsinformation om användare och grupphantering|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuella & molntjänster](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Windows-händelseloggen & Linux Syslog|    Samlar in systemdata och loggningsdata på virtuella datorer och överför data till ett lagringskonto som du väljer.|   Windows använder [BOMULLSTUSS](https://docs.microsoft.com/azure/azure-diagnostics) (Windows Azure-diagnostik lagring) och Linux i Azure-monitor|
|[Lagringsanalys](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Loggning för lagring och ger mätvärdesdata för ett lagringskonto|Ger inblick i trace-begäranden, analysera användningstrender och diagnostisera problem med ditt lagringskonto.|    REST API eller [klientbibliotek](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Loggar för flödet av NSG (Nätverkssäkerhetsgrupp)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON-format och visar utgående och inkommande flöden på grundval av per regel|Visa information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp|[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Loggar, undantag och anpassade diagnostik|    Performance Management (APM) programtjänsten för webbutvecklare på flera plattformar.| REST API [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Bearbeta Data / Säkerhetsvarning| Azure Security Center-avisering, OMS-avisering| Information om säkerhet och aviseringar.|   REST API: er, JSON|

### <a name="activity-log"></a>Aktivitetslogg
Den [Azure-aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), ger inblick i åtgärder som utfördes på resurser i din prenumeration. Aktivitetsloggen kallades tidigare ”granskningsloggar” eller ”operativa loggar” eftersom den rapporterar [kontroll-plan händelser](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) för dina prenumerationer. Använder aktivitetsloggen, kan du bestämma den ”vad som, och när” för alla skrivåtgärder (PUT, POST, ta bort) tas för de resurser i din prenumeration. Du kan också få status för åtgärden och andra relevanta egenskaper. Aktivitetsloggen inkluderar inte läsåtgärder (GET).

Här avser PUT, POST, ta bort alla aktivitetsloggen skrivåtgärder operations innehåller på resurser. Du kan till exempel använda aktivitetsloggarna att söka efter ett fel när du felsöker eller för att övervaka hur en användare i din organisation ändrades en resurs.

![Aktivitetslogg](./media/azure-log-audit/azure-log-audit-fig1.png)


Du kan hämta händelser från din aktivitetsloggen med hjälp av Azure portal [CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), PowerShell-cmdlets och [REST-API för Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Aktivitetsloggar har 19 dagars datakvarhållningsperiod.

Integrationsscenarier
-   [Skapa en e-post eller webhook avisering som utlöses av en aktivitet händelselogg.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [Strömma till en Händelsehubb](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) för införandet av en tjänst från tredje part eller anpassade analytics lösning, till exempel PowerBI.

-   Analysera i PowerBI med hjälp av den [PowerBI-Innehållspaketet.](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)

-   [Spara den till ett Lagringskonto för arkivering eller Manuell kontroll.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log) Du kan ange kvarhållningstiden (i dagar) med hjälp av loggen profiler.

-   Fråga efter och visa den i Azure-portalen.

-   Fråga den via PowerShell-cmdleten, CLI eller REST API.

-   Exportera aktivitetsloggen med Log-profiler till [logga Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Du kan använda ett lagringskonto eller [event hub namnområde](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) som inte är i samma prenumeration som en ljusavgivande loggen. Den användare som konfigurerar inställningen måste ha rätt [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) åtkomst till båda prenumerationer
### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar
Azure diagnostikloggar orsakat av en resurs som innehåller omfattande, ofta data om användningen av den här resursen. Innehållet i de här loggarna varierar efter resurstyp (till exempel [Windows-händelsesystemloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)är en kategori av diagnostiska loggar för virtuella datorer och [blob-, tabell- och kön loggar](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) finns kategorier av diagnostiska loggar för storage-konton) och skiljer sig från aktivitetsloggen, som ger inblick i åtgärder som utfördes på resurser i din prenumeration.

![Azure diagnostikloggar](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure Diagnostics loggar erbjuder flera konfigurationsalternativ som är, Azure-portalen med hjälp av PowerShell-kommandoradsgränssnittet (CLI) och REST-API.

**Integrationsscenarier**
-   Spara dem på en [Lagringskonto](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) för granskning eller Manuell kontroll. Du kan ange kvarhållningstiden (i dagar) med hjälp av inställningarna för diagnostik.

-   [Strömma dem i Händelsehubbar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) för införandet av en tjänst från tredje part eller anpassade analytics lösning som [PowerBI.](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

-   Analysera dem med [OMS logganalys.](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

**Tjänster som stöds, schemat för diagnostikloggar och stöds loggen kategorier per resurstyp**


| Tjänst | Schemat & Docs | Resurstyp | Kategori |
| ------- | ------------- | ------------- | -------- |
|Belastningsutjämnare| [Logganalys för Azure-belastningsutjämnaren (förhandsgranskning)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|Nätverkssäkerhetsgrupper|[Log Analytics för nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Application Gateways|[Diagnostikloggning för Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Key Vault|[Azure Key Vault-loggning](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Att aktivera och använda Sök trafik Analytics](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Data Lake Store|[Åtkomst till diagnostikloggarna för Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Granska|
|Data Lake Analytics|[Åtkomst till diagnostikloggar för Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|Granska|
|||Microsoft.DataLakeAnalytics/accounts|Begäranden|
|||Microsoft.DataLakeStore/accounts|Begäranden|
|Logic Apps|[Anpassat Logic Apps B2B-spårningsschema](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch-diagnostikloggning](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Logganalys för Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Händelsehubbar|[Azure Event Hubs diagnostiska loggar](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[Jobbet diagnostikloggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|Körnings-|
|||Microsoft.StreamAnalytics/streamingjobs|Redigering|
|Service Bus|[Azure Service Bus-diagnostikloggar](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory Reporting
Azure AD (Active Directory Azure) tillhandahåller säkerhets-, aktivitets- och granskningsrapporter för din katalog. Den [Azure Active Directory-kontrollrapport](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) hjälper kunder att identifiera Privilegierade åtgärder som uppstått i sina Azure Active Directory. Privilegierade åtgärder omfattar höjning ändringar (till exempel skapa användarrollen eller återställning av lösenord), ändra principkonfigurationer (till exempel lösenordsprinciper) eller ändringar av katalogkonfiguration (till exempel ändringar i federation domäninställningar).

Med rapporterna får granskningsposten för händelsenamnet aktören som utförde åtgärden målresursen påverkas av ändringen, samt datum och tid (UTC). Kunder kan hämta listan över granskningshändelser för sina Azure Active Directory via den [Azure-portalen](https://portal.azure.com/), enligt beskrivningen i [visa granskningsloggarna](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Här är en lista över de rapporter som ingår:

| Säkerhetsrapporter | Aktivitetsrapporter | Granskningsrapporter |
| :--------------- | :--------------- | :------------ |
|Inloggningar från okända källor| Programanvändning: sammanfattning| Kataloggranskningsrapport|
|Inloggningar efter flera fel|  Programanvändning: detaljerad||
|Inloggningar från flera geografiska områden|    Instrumentpanel för program||
|Inloggningar från IP-adresser med misstänkt aktivitet|   Kontoetableringsfel||
|Oregelbunden inloggningsaktivitet|    Enskilda användarenheter||
|Inloggningar från potentiellt infekterade enheter|   Aktivitet för enskilda användare||
|Användare med avvikande inloggningsaktivitet| Aktivitetsrapport för grupper||
||Aktivitetsrapport över registrering av lösenordsåterställning||
||Lösenordsåterställningsaktivitet|||

Data för de här rapporterna kan vara användbara för ditt program, till exempel SIEM-system, granskning och business intelligence-verktyg. Azure AD reporting API: er ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa dessa [API: er](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) från olika programmeringsspråk språk och verktyg.

Händelser i Azure AD granska rapporten finns kvar i 180 dagar.

> [!Note]
> Mer information om kvarhållning i rapporter finns [Azure Active Directory Rapportkvarhållningsregler.](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)

För kunder som vill lagra sina granskningshändelser under längre bevarandeperioder, Reporting-API kan användas för att regelbundet hämtar [granskningshändelser](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) i ett separat datalager.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Loggar för virtuell dator med hjälp av Azure-diagnostik
[Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) är funktion i Azure som möjliggör insamling av diagnostikdata på ett distribuerat program. Du kan använda tillägget diagnostik från flera olika källor. Stöds för närvarande är [Azure Cloud Service webb- och arbetsroller](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me),

![Loggar för virtuell dator med hjälp av Azure-diagnostik](./media/azure-log-audit/azure-log-audit-fig3.png)

[Virtuella Azure-datorer](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) med Microsoft Windows och [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

Du kan aktivera Azure diagnostisk på en virtuell dator med hjälp av följande:

-   Med hjälp av Visual Studio finns [använda Visual Studio för att spåra Azure virtuella datorer](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

-   [Konfigurera Azure-diagnostik på en Azure virtuell dator från en fjärrdator](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [Använd PowerShell för att ställa in diagnostik på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Skapa en Windows virtuell dator med övervakning och diagnostik med Azure Resource Manager-mall](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Lagringsanalys
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) utför loggning och tillhandahåller data för mått för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot. Storage Analytics loggning är tillgänglig för den [Blob-, kö-och tabellen.](https://docs.microsoft.com/azure/storage/storage-introduction) Storage Analytics loggar detaljerad information om lyckade och misslyckade förfrågningar till en storage-tjänst.

Den här informationen kan användas för att övervaka enskilda begäranden och diagnostisera problem med en storage-tjänst. Begäranden loggas för bästa prestanda. Loggposter skapas bara om det finns begäranden som görs mot tjänstslutpunkten. Om ett lagringskonto har aktivitet i sin slutpunkt för Blob men inte i tabellen eller kö slutpunkter, endast loggar rör skapa Blob-tjänsten.

Om du vill använda Storage Analytics, måste du aktivera den separat för varje tjänst som du vill övervaka. Du kan aktivera den i den [Azure-portalen](https://portal.azure.com/); mer information finns i [övervaka ett lagringskonto i Azure-portalen.](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Du kan använda åtgärden Ange egenskaper för tjänsten för att aktivera Storage Analytics individuellt för varje tjänst.

Sammanställda data lagras i en välkänd blob (för loggning) och välkända tabeller (för mått), som kan nås med hjälp av Blob-tjänsten och tabelltjänsten API: er.

Storage Analytics har en 20 TB gräns på mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Alla loggar lagras i [blockblobbar](https://docs.microsoft.com/azure/storage/storage-analytics) i en behållare med namnet $logs, som skapas automatiskt när Storage Analytics har aktiverats för ett lagringskonto.

> [!Note]
> Mer information om fakturering och datakvarhållningsprinciper finns [Storage Analytics och fakturering.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)
>
> [!Note]
> Läs mer om lagringskontogränser [och prestandamål för Azure Storage skalbarhet.](https://docs.microsoft.com/azure/storage/storage-scalability-targets)

Följande typer av autentiserade och anonyma begäranden loggas.



| Autentiserad  | Anonym|
| :------------- | :-------------|
| Lyckade begäranden | Lyckade begäranden |
|Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel | Begäranden som använder en delad signatur åtkomst (SAS), inklusive misslyckade och lyckade begäranden |
| Begäranden som använder en delad signatur åtkomst (SAS), inklusive misslyckade och lyckade begäranden |Timeout-fel för både klient och server |
|   Begäranden till analysdata |    Misslyckade GET-begäranden med felkoden 304 (ändra inte) |
| Förfrågningar som görs av Storage Analytics, till exempel loggen skapas eller tas bort, loggas inte. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformatet](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) avsnitt. | Alla andra misslyckade anonyma begäranden har inte loggats. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformatet](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Azure-nätverk loggar
Loggning och nätverksövervakning i Azure är omfattande och omfattar två kategorier:

-   [Nätverk Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) -scenariobaserade nätverksövervakning tillhandahålls med funktionerna i Nätverksbevakaren. Den här tjänsten innehåller paketinsamling, nästa hopp, IP-flöde Kontrollera NSG flödet loggar i gruppvyn säkerhet. Scenariot nivån övervakning innehåller en heltäckande vy av nätverksresurser i stället för enskilda resurs nätverksövervakning.

-   [Resursövervakning](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) -nivå Resursövervakning består av fyra funktioner, diagnostiska loggar, mått, felsökning och resurshälsa. Alla dessa funktioner är byggda på resursen nätverksnivån.

![Azure-nätverk loggar](./media/azure-log-audit/azure-log-audit-fig4.png)

Nätverksbevakaren är en regionala tjänst som gör att du kan övervaka och diagnostisera villkor på nätverket scenariot nivå, till och från Azure. Diagnostiska nätverks- och visualiseringsverktyg som finns tillgängliga med Nätverksbevakaren hjälpa dig att förstå, diagnostisera och få insyn i nätverket i Azure.

**NSG flöda loggning** -flöde Nätverkssäkerhetsgrupper loggarna gör det möjligt att samla in loggar som rör trafik som tillåts eller nekas av säkerhetsregler i gruppen. Loggarna flödet skrivs i JSON-format och visa utgående och inkommande flöden på grundval av per regel, NIC flödet gäller för 5-tuppel information om flödet (källan/målet IP-källan/målet Port Protocol), och om trafiken tillåts eller nekas.

### <a name="network-security-group-flow-logging"></a>Nätverkssäkerhetsgruppen flöde loggning

[Nätverkssäkerhetsgruppen flöde loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) är en funktion i Nätverksbevakaren där du kan visa information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp. Loggarna flödet skrivs i JSON-format och visa utgående och inkommande flöden på grundval av per regel, NIC flödet gäller för 5-tuppel information om flödet (källan/målet IP-källan/målet Port Protocol), och om trafiken tillåts eller nekas.

Medan flödet loggar mål Nätverkssäkerhetsgrupper, som inte visas samma som de andra loggarna. Flödet loggfilerna lagras i ett lagringskonto.

Samma bevarandeprinciper som visas på andra loggar gäller flödet loggar. Loggar har en bevarandeprincip som kan anges från dag 1 och 365 dagar. Om en bevarandeprincip inte är inställd bevaras loggarna för evigt.

**Diagnostikloggar**

Periodiska och eget initiativ händelser skapas av nätverksresurser och inloggad storage-konton, skickas till en Händelsehubb eller logganalys. Dessa loggar ger insikter om hälsotillståndet för en resurs. Dessa loggar kan visas i verktyg som Power BI och logganalys. Ta reda på hur du visar diagnostikloggar [logganalys.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)

![Diagnostikloggar](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnostikloggar är tillgängliga för [belastningsutjämnaren](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), vägar och [Application Gateway.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

Nätverksbevakaren ger diagnostikloggar vyn. Den här vyn innehåller alla nätverksresurser som stöder diagnostikloggning. I den här vyn kan du aktivera och inaktivera nätverksresurser lätt och snabbt.


Utöver föregående loggningsfunktioner har Nätverksbevakaren för närvarande följande funktioner:
- [Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -nätverket nivån visa visar de olika anslutningarna och associationer mellan nätverksresurser i en resursgrupp.

- [Variabeln paketinsamling](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -samlar in paketdata till och från en virtuell dator. Avancerade alternativ för filtrering och finjustera kontroller, till exempel att kunna ange tid och storlek begränsningar ger flexibilitet. Paketdata kan lagras i en blobstore eller på den lokala disken i CAP-format.

-   [IP-flöde verifierar](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) -kontrollerar om ett paket tillåts eller nekas baserat på flödet information 5-tuppel paket parametrar (mål-IP, käll-IP, målport, källport och Protocol). Om paketet nekas av en säkerhetsgrupp, returneras regeln och grupp som nekas paketet.

-   [Nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) -avgör nästa hopp för paket som vidarebefordras i Azure-nätverksinfrastruktur, vilket gör det lättare att diagnostisera eventuella felkonfigurerat användardefinierade vägar.

-   [Säkerhet gruppvyn](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -hämtar effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.

-   [Virtuella Nätverksgatewayen och anslutningen felsökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) – ger dig möjlighet att felsöka virtuella Nätverksgatewayer och anslutningar.

-   [Nätverk prenumerationsbegränsningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits) -kan du visa nätverksresursanvändning mot gränser.

### <a name="application-insight"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar Management-program (APM)-tjänst för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Det är att automatiskt identifiera prestandaavvikelser. Den inkluderar kraftfulla analysverktyg för att hjälpa dig diagnosticera problem och förstå vad användare faktiskt gör med din app.

 Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

 Den fungerar för appar på en rad olika plattformar, däribland .NET, Node.js och J2EE som finns lokalt eller i molnet. Den kan integreras med devOps-processen och har kopplingspunkter till olika utvecklingsverktyg.

![Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights är avsett för utvecklingsteamet och gör det lättare att förstå hur appen fungerar och hur den används. Tjänsten övervakar:

-   **Begärandefrekvens, svarstider och felfrekvens** – Ta reda på vilka sidor som är mest populära, vid vilka tidpunkter på dagen och var dina användare finns. Se vilka sidor som fungerar bäst. Om svarstiden och felfrekvensen är hög när det finns många begäranden kan det bero på ett resurstilldelningsproblem.

-   **Beroendefrekvens, svarstider och felfrekvens** – Ta reda på om externa tjänster gör systemet långsammare.

-   **Undantag** – analysera sammanställda statistik, eller välja specifika instanser och detaljerat stackspårning och relaterade begäranden. Både server- och webbläsarundantag rapporteras.

-   **Sidvyer och inläsningsprestanda** – Rapporteras av användarnas webbläsare.

-   **AJAX-anrop** från webbsidor – frekvens, svarstider och felfrekvens.

-   **Antal användare och sessioner**.

-   **Prestandaräknare** från dina Windows- eller Linux-serverdatorer, till exempel processor, minne och nätverksanvändning.

-   **Värddiagnostik** från Docker eller Azure.

-   **Diagnostikspårningsloggar** från din app – så att du kan jämföra spårningshändelser med begäranden.

-   **Anpassade händelser och mätvärden** som du skriver själv i klient- eller serverkoden för att spåra affärshändelser, t.ex. sålda artiklar eller vunna spel.

**Lista över integrationsscenarier och beskrivning:**

| Integrationsscenarier | Beskrivning |
| --------------------- | :---------- |
|[Programavbildningen](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Komponenterna i din app, med viktiga mätvärden och aviseringar.||
|[Diagnostiska Sök till exempel data](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Sök efter och filtrera händelser, till exempel begäranden, undantag, beroendeanrop, loggspårningar och sidvyer.||
|[Metrics Explorer för aggregerade data](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Utforska, filtrera och segmentera aggregerade data, till exempel begärande-, fel- och undantagsfrekvens, svarstider och sidinläsningstider.||
|[Instrumentpaneler](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Kombinera data från flera resurser och dela med andra. Perfekt för program med flera komponenter och för kontinuerlig visning i teamutrymmet.||
|[Direktsänd dataström mått](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|När du distribuerar en ny version kan du titta på dessa prestandaindikatorer som visas i realtid för att kontrollera att allt fungerar som förväntat.||
|[Analys](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Besvara svåra frågor om appens prestanda och användning med hjälp av det här kraftfulla frågespråket.||
|[Automatisk och manuell aviseringar](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatiska aviseringar är anpassade efter appens normala telemetrimönster och visas vid händelser som avviker från det vanliga mönstret. Du kan också ställa in aviseringar på särskilda nivåer med anpassade mätvärden eller standardmätvärden.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Visa prestandadata i koden. Gå till kod från stackspårningar.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrera användningsmätvärden med annan Business Intelligence.||
|[REST API](https://dev.applicationinsights.io/)|Skriv kod för att köra frågor mot dina mätvärden och rådata.||
|[Löpande export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Massredigera export av rådata till lagring när det kommer fram.||

### <a name="azure-security-center-alerts"></a>Azure Security Center Alerts
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) automatiskt samlar in, analyseras och integreras loggdata från resurserna i Azure, nätverket och anslutna partnerlösningar som brandväggen och endpoint protection lösningar är att identifiera verkliga hot och minska false positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp.

Hotidentifieringen i Security Center sker genom automatisk insamling av säkerhetsinformation från dina Azure-resurser, nätverket och anslutna partnerlösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas.

![Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Genombrott i stora mängder data och [maskininlärning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tekniker används för att utvärdera händelser över hela molninfrastrukturresurs – upptäcka hot som skulle vara omöjligt att identifiera med manuella metoder och förutsäga utvecklingen av attacker. Dessa säkerhetsanalyser omfattar:

-   **Integrerad hotinformation:** söker efter kända obehöriga genom att använda globala hotinformation från Microsoftprodukter och tjänster, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa flöden används.

-   **Beteendeanalys:** gäller kända mönster för att identifiera skadligt beteende.

-   **Avvikelseidentifiering:** använder statistiska profilering för att bygga en historisk baslinje. Den här typen av identifiering varnar vid avvikelser från upprättade baslinjer som matchar vektorn för ett potentiellt angrepp.


Många säkerhetsåtgärder och incidenter team förlitar sig på en lösning för Security Information and Event Management (SIEM) som startpunkt för triaging och undersöker säkerhetsaviseringar. Med Azure logganalys-integration kan kunder synkronisera varningsmeddelanden och virtuella säkerhetshändelser samlas in av Azure-diagnostik och Azure-granskningsloggarna med deras logganalys eller SIEM-lösning i nära realtid.


## <a name="log-analytics"></a>Log Analytics

Log Analytics är en tjänst i [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) som hjälper dig att samla in och analysera data som genereras av resurser i molnet och lokala miljöer. Den ger dig realtidsinsikter som använder integrerad sökning och anpassade instrumentpaneler för att analysera lätt miljontals poster för alla arbetsbelastningar och servrar oavsett fysiska plats.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

I mitten av de Log Analytics är OMS-databasen, som finns i Azure-molnet. Data samlas i databasen från anslutna källor genom att konfigurera datakällor och lägga till lösningar i din prenumeration. Alla datakällor och lösningar skapar olika typer av poster som har en egen uppsättning egenskaper, men de kan fortfarande analyseras tillsammans i förfrågningar till databasen. Det gör att du kan använda samma verktyg och metoder för att arbeta med olika typer av data som samlats från olika källor.

Anslutna datakällor är de datorer och andra resurser som genererar data som samlas in av Log Analytics. Detta kan inkludera agenter som installerats på [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) och [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) datorer som ansluter direkt eller agenter i [en ansluten hanteringsgrupp för System Center Operations Manager.](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents) Log Analytics kan också samla in data från [Azure storage.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)

[Datakällor](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) är olika typer av data som samlas in från varje anslutna källa. Detta innefattar händelser och [prestandadata](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) från [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) och Linux-agenter förutom källor som [IIS-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs), och [anpassade textloggar.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs) Du kan konfigurera varje datakälla som du vill samla in och konfigurationen skickas automatiskt till varje ansluten källa.

Det finns fyra olika sätt att [att samla in loggar och mått för Azure-tjänster:](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)
1.  Azure diagnostics direkt till logganalys (diagnostik i tabellen nedan)

2.  Azure diagnostics till Azure-lagring till logganalys (lagring i följande tabell)

3.  Kopplingar för Azure-tjänster (kopplingar i följande tabell)

4.  Skript för att samla in och sedan skicka data till logganalys (tomma celler i tabellen nedan och för tjänster som inte anges)

| Tjänst | Resurstyp | Logs | Mått | Lösning |
| :------ | :------------ | :--- | :------ | :------- |
|Programgateways|  Microsoft.Network/<br>applicationGateways|  Diagnostik|Diagnostik|    [Azure Application](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Anslutning|  Anslutning|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [anslutningsprogrammet (förhandsversion)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Automation-konton|   Microsoft.Automation/<br>AutomationAccounts|    Diagnostik||       [Mer information](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Batch-konton|    Microsoft.Batch/<br>batchAccounts|  Diagnostik|    Diagnostik||
|Klassiska molntjänster||       Lagring||       [Mer information](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>konton|       Diagnostik|||
|Data Lake analytics|   Microsoft.DataLakeAnalytics/<br>konton|   Diagnostik|||
|Data Lake store|   Microsoft.DataLakeStore/<br>konton|   Diagnostik|||
|Namnområde för händelsehubb|   Microsoft.EventHub/<br>namnområden|  Diagnostik|    Diagnostik||
|IoT-hubbar|  Microsoft.Devices/<br>IotHubs||     Diagnostik||
|Key Vault| Microsoft.KeyVault/<br>Valv|  Diagnostik  || [KeyVault Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Belastningsutjämning|    Microsoft.Network/<br>loadBalancers|    Diagnostik|||
|Logic Apps|    Microsoft.Logic/<br>Arbetsflöden|  Diagnostik|    Diagnostik||
||Microsoft.Logic/<br>integrationAccounts||||
|Nätverkssäkerhetsgrupper|   Microsoft.Network/<br>networksecuritygroups|Diagnostik||   [Azure Nätverkssäkerhetsgruppen Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Recovery-valv|   Microsoft.RecoveryServices/<br>Valv|||[Azure Recovery Services Analytics (förhandsgranskning)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Söktjänster|   Microsoft.Search/<br>searchServices|    Diagnostik|    Diagnostik||
|Service Bus-namnområde| Microsoft.ServiceBus/<br>namnområden|    Diagnostik|Diagnostik|    [Service Bus Analytics (förhandsgranskning)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Lagring||    [Service Fabric Analytics (förhandsgranskning)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>databaser||       Diagnostik||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Lagring|||         Skript| [Azure Storage Analytics (förhandsgranskning)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Virtuella datorer|  Microsoft.Compute/<br>virtualMachines|  Anknytning|  Anknytning||
||||Diagnostik||
|Skalningsuppsättningar i virtuella datorer|   Microsoft.Compute/<br>virtualMachines    ||Diagnostik||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Webbservergrupper|Microsoft.Web/<br>serverfarms||   Diagnostik
|Webbplatser| Microsoft.Web/<br>webbplatser ||      Diagnostik|    [Mer information](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>platser /<br>fack|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Log-integrering med lokala SIEM-system
[Azure logganalys integration](https://www.microsoft.com/download/details.aspx?id=53324) gör det möjligt att integrera loggarna från Azure-resurser i till din lokala **Security Information and Event Management (SIEM) system**.

![Log-integrering](./media/azure-log-audit/azure-log-audit-fig9.png)

Azure logganalys integration samlar in Azure-diagnostik från Windows (BOMULLSTUSS) virtuella datorer, Azure Security Center-aviseringar i aktivitetsloggar Azure och Azure Resource Provider loggar. Den här integreringen ger en enhetlig instrumentpanel för alla dina tillgångar, lokalt eller i molnet, så att du kan aggregera, korrelera, analysera och varna för säkerhetshändelser.



Azure logganalys-integration stöder för närvarande integrering av aktivitetsloggar Azure, Windows-händelselogg från Windows-dator i Azure-prenumerationen, Azure Security Center-aviseringar, Azure diagnostikloggar och Azure Active Directory-granskningsloggar.

| Loggtyp | Logganalys stöder JSON (Splunk ArcSight, Qradar) |
| :------- | :-------------------------------------------------------- |
|AAD-granskningsloggar|    ja|
|Aktivitetsloggar| Ja|
|ASC aviseringar |Ja|
|Diagnostik-loggar (resurs-loggarna)|  Ja|
|VM-loggar|   Ja via vidarebefordrade händelser och inte via JSON|


I följande tabell beskriver loggen kategori och information om integrering av SIEM.

[Kom igång med Azure logganalys-integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) – självstudier vägleder dig genom installationen av Azure logganalys-integrering och integrera loggar från Azure BOMULLSTUSS lagring, Azure aktivitetsloggar Azure Security Center-aviseringar och Azure Active Directory granskningsloggar.

Integrationsscenarier

-   [Samarbeta konfigurationssteg](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – det här blogginlägget visar hur du konfigurerar Azure logganalys-integrering ska fungera med partnerlösningar Splunk, HP ArcSight och IBM QRadar.

-   [Azure logganalys Integration vanliga frågor (FAQ)](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) – det här avsnittet får du svar frågor om Azure logganalys-integration.

-   [Integrera Security Center aviseringar med Azure logga Integration](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) – det här dokumentet beskrivs hur du synkroniserar varningsmeddelanden, tillsammans med virtuella säkerhetshändelser samlas in av Azure-diagnostik och Azure-granskningsloggarna med din logganalys eller SIEM-lösning.

## <a name="next-steps"></a>Nästa steg

- [Granskning och loggning](https://www.microsoft.com/trustcenter/security/auditingandlogging)

Skydda data genom att underhålla synlighet och agera snabbt i rimlig säkerhetsaviseringar

- [Loggning och granskning Logginsamling i Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Vilka inställningar du behöver tillämpa för att kontrollera att din Azure-instanser att samla in rätt säkerhet och granskningsloggar.

- [Konfigurera granskningsinställningar för en webbplatssamling](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

Som administratör för en webbplatssamling, en kan hämta historik över åtgärder som vidtas för en viss användare och kan också hämta en historik över åtgärder under ett visst datumintervall. 

- [Söka granskningsloggen i Office 365 säkerhets- och Efterlevnadscentret](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

En kan använda Office 365 säkerhets- och Efterlevnadscentret för att söka enhetlig granskningsloggen för att visa aktiviteten för användare och administratör i din Office 365-organisation.


