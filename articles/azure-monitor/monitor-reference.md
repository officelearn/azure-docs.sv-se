---
title: Vad övervakas av Azure Monitor
description: Referens för alla tjänster och andra resurser som övervakas av Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 055ba8b5050aef639bbe6527292a18c8b405065a
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620262"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Vad övervakas av Azure Monitor?
I den här artikeln beskrivs de olika program och tjänster som övervakas av Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Insikter och huvud lösningar
Grundläggande insikter och lösningar betraktas som en del av Azure Monitor och följer support-och service nivå avtalen för Azure. De stöds i alla Azure-regioner där Azure Monitor är tillgängligt.

### <a name="insights"></a>Insikter

Insikter ger en anpassad övervaknings miljö för specifika program och tjänster. De samlar in och analyserar både loggar och mått.

| Insight | Beskrivning |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Utöknings bar APM-tjänst (Application Performance Management) för att övervaka ditt Live-webbprogram på valfri plattform. |
| [Azure Monitor för behållare](insights/container-insights-overview.md) | Övervakar prestanda för behållar arbets belastningar som distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). |
| [Azure Monitor för Cosmos DB (förhands granskning)](insights/cosmosdb-insights-overview.md) | Innehåller en översikt över övergripande prestanda, fel, kapacitet och drifts hälsa för alla dina Azure Cosmos DB resurser i en enhetlig interaktiv upplevelse. |
| [Azure Monitor för nätverk (för hands version)](insights/network-insights-overview.md) | Innehåller en omfattande vy över hälso tillstånd och mått för alla nätverks resurser. Med den avancerade Sök funktionen kan du identifiera resurs beroenden, aktivera scenarier som att identifiera resurser som är värdar för din webbplats genom att helt enkelt söka efter namnet på din webbplats. |
[Azure Monitor för resurs grupper (förhands granskning)](insights/resource-group-insights.md) |  Prioritering och diagnostisera eventuella problem som kan uppstå för enskilda resurser, samtidigt som den erbjuder kontexten och prestandan för resurs gruppen som helhet. |
| [Azure Monitor för lagring (för hands version)](insights/storage-insights-overview.md) | Tillhandahåller omfattande övervakning av dina Azure Storage konton genom att leverera en enhetlig vy över dina Azure Storages prestanda, kapacitet och tillgänglighet. |
| [Azure Monitor for VMs (för hands version)](insights/container-insights-overview.md) | Övervakar dina virtuella Azure-datorer (VM) och skalnings uppsättningar för virtuella datorer i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer. |

### <a name="core-solutions"></a>Kärn lösningar

Lösningar baseras på logg frågor och vyer som är anpassade för ett visst program eller en viss tjänst. De samlar in och analyserar bara loggar och är föråldrade med tiden för insikter.

| Lösning | Beskrivning |
|:---|:---|
| [Agent hälsa](insights/solution-agenthealth.md) | Analysera hälsan och konfigurationen av Log Analyticss agenter. |
| [Varningshantering](platform/alert-management-solution.md) | Analysera aviseringar som samlats in från System Center Operations Manager, nagios eller zabbix. |
| [Tjänstkarta](insights/service-map.md) | Identifierar automatiskt program komponenter i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. |



## <a name="azure-services"></a>Azure-tjänster
I följande tabell visas Azure-tjänster och de data som samlas in i Azure Monitor. 

- Mått – tjänsten samlar automatiskt in mått i Azure Monitor Mät värden. 
- Loggar – tjänsten stöder diagnostikinställningar som kan samla in plattforms loggar och statistik för att Azure Monitor loggar.
- Insight – det finns en insikt tillgänglig för tjänsten som tillhandahåller en anpassad övervaknings upplevelse för tjänsten.

| Tjänst | Mått | Loggar | Insight | Anteckningar |
|:---|:---|:---|:---|:---|
|Active Directory | Nej | Ja | [Ja](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nej | Nej | Nej |  |
|Active Directory Domain Services | Nej | Ja | Nej |  |
|Aktivitetslogg | Nej | Ja | Nej | |
|Advanced Threat Protection | Nej | Nej | Nej |  |
|Advisor | Nej | Nej | Nej |  |
|AI-verktyg | Nej | Nej | Nej |  |
|Analysis Services | Ja | Ja | Nej |  |
|API för FHIR | Nej | Nej | Nej |  |
|API Management | Ja | Ja | Nej |  |
|App Service | Ja | Ja | Nej |  |
|AppConfig | Nej | Nej | Nej |  |
|Application Gateway | Ja | Ja | Nej |  |
|Attesterings tjänst | Nej | Nej | Nej |  |
|Automation | Ja | Ja | Nej |  |
|Azure Service Manager (RDFE) | Nej | Nej | Nej |  |
|Backup | Nej | Ja | Nej |  |
|Bastion | Nej | Nej | Nej |  |
|Batch | Ja | Ja | Nej |  |
|Batch AI | Nej | Nej | Nej |  |
|Blockchain Service | Nej | Ja | Nej |  |
|Skisser | Nej | Nej | Nej |  |
|Robottjänst | Nej | Nej | Nej |  |
|Cloud Services | Ja | Ja | Nej | Agent som krävs för att övervaka gäst operativ system och arbets flöden.  |
|Cloud Shell | Nej | Nej | Nej |  |
|Cognitive Services | Ja | Ja | Nej |  |
|Container Instances | Ja | Nej | Nej |  |
|Container Registry | Ja | Ja | Nej |  |
|Content Delivery Network (CDN) | Nej | Ja | Nej |  |
|Cosmos DB | Ja | Ja | [Ja](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nej | Nej | Nej |  |
|Data Box | Nej | Nej | Nej |  |
|Data Catalog Gen2 | Nej | Nej | Nej |  |
|Datautforskaren | Ja | Ja | Nej |  |
|Data Factory | Ja | Ja | Nej |  |
|Data Factory v2 | Nej | Ja | Nej |  |
|Data resurs | Nej | Nej | Nej |  |
|Databas för MariaDB | Ja | Ja | Nej |  |
|Databas för MySQL | Ja | Ja | Nej |  |
|Databas för PostgreSQL | Ja | Ja | Nej |  |
|Database Migration Service | Nej | Nej | Nej |  |
|Databricks | Nej | Ja | Nej |  |
|DDoS Protection | Ja | Ja | Nej |  |
|DevOps | Nej | Nej | Nej |  |
|DNS | Ja | Nej | Nej |  |
|Domän namn | Nej | Nej | Nej |  |
|– | Nej | Nej | Nej |  |
|Dynamics 365 kund engagemang | Nej | Nej | Nej |  |
|Dynamics 365-ekonomi och-åtgärder | Nej | Nej | Nej |  |
|Event Grid | Ja | Nej | Nej |  |
|Event Hubs | Ja | Ja | Nej |  |
|ExpressRoute | Ja | Ja | Nej |  |
|Brandvägg | Ja | Ja | Nej |  |
|Front Door | Ja | Ja | Nej |  |
|Functions | Ja | Ja | Nej |  |
|HDInsight | Nej | Ja | Nej |  |
|HPC-cache | Nej | Nej | Nej |  |
|Informationsskydd | Nej | Ja | Nej |  |
|Intune | Nej | Ja | Nej |  |
|IoT Central | Nej | Nej | Nej |  |
|IoT Hub | Ja | Ja | Nej |  |
|Key Vault | Ja | Ja | Nej |  |
|Kubernetes Service (AKS) | Nej | Nej | [Ja](insights/container-insights-overview.md)  |  |
|Lastbalanserare | Ja | Ja | Nej |  |
|Logic Apps | Ja | Ja | Nej |  |
|Machine Learning Service | Nej | Nej | Nej |  |
|Hanterade program  | Nej | Nej | Nej |  |
|Maps  | Nej | Nej | Nej |  |
|Media Services | Ja | Ja | Nej |  |
|Microsoft Flow | Nej | Nej | Nej |  |
|Microsoft-hanterat skriv bord | Nej | Nej | Nej |  |
|Microsoft PowerApps | Nej | Nej | Nej |  |
|Microsoft socialt engagemang | Nej | Nej | Nej |  |
|Microsoft Stream | Ja | Ja | Nej |  |
|Migrera | Nej | Nej | Nej |  |
|Multi-Factor Authentication | Nej | Ja | Nej |  |
|Network Watcher | Ja | Ja | Nej |  |
|Notification Hubs | Ja | Nej | Nej |  |
|Open Datasets | Nej | Nej | Nej |  |
|Princip | Nej | Nej | Nej |  |
|Power BI | Ja | Ja | Nej |  |
|Power BI Embedded | Nej | Nej | Nej |  |
|Private Link | Nej | Nej | Nej |  |
|Kommunikations plattform för bufferthanterare | Nej | Nej | Nej |  |
|Red Hat OpenShift | Nej | Nej | Nej |  |
|Redis Cache | Ja | Ja | Nej |  |
|Resursgraph | Nej | Nej | Nej |  |
|Resource Manager | Nej | Nej | Nej |  |
|Butiks sökning – efter Bing | Nej | Nej | Nej |  |
|Söka | Ja | Ja | Nej |  |
|Service Bus | Ja | Ja | Nej |  |
|Service Fabric | Nej | Ja | Nej | Agent som krävs för att övervaka gäst operativ system och arbets flöden.  |
|Registrerings Portal | Nej | Nej | Nej |  |
|Webbplatsåterställning | Nej | Ja | Nej |  |
|Vår moln tjänst | Nej | Nej | Nej |  |
|SQL Data Warehouse | Ja | Ja | Nej |  |
|SQL Database | Ja | Ja | Nej |  |
|SQL Server Stretch Database | Ja | Ja | Nej |  |
|Stack | Nej | Nej | Nej |  |
|Storage | Ja | Nej | [Ja](insights/storage-insights-overview.md) |  |
|Storage cache | Nej | Nej | Nej |  |
|Tjänster för synkronisering av lagring | Nej | Nej | Nej |  |
|Stream Analytics | Ja | Ja | Nej |  |
|Time Series Insights | Ja | Ja | Nej |  |
|TINA | Nej | Nej | Nej |  |
|Traffic Manager | Ja | Ja | Nej |  |
|Universell utskrift | Nej | Nej | Nej |  |
|Virtual Machine Scale Sets | Nej | Ja | [Ja](insights/vminsights-overview.md) | Agent som krävs för att övervaka gäst operativ system och arbets flöden. |
|Virtuella datorer | Ja | Ja | [Ja](insights/vminsights-overview.md) | Agent som krävs för att övervaka gäst operativ system och arbets flöden. |
|Virtual Network | Ja | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtual Network NSG flödes loggar | Nej | Ja | Nej |  |
|VPN Gateway | Ja | Ja | Nej |  |
|Windows Virtual Desktop | Nej | Nej | Nej |  |


## <a name="product-integrations"></a>Produkt integreringar
Tjänsterna och lösningarna i följande tabell lagrar sina data på en Log Analytics arbets yta så att den kan analyseras med andra loggdata som samlas in av Azure Monitor.

| Produkt/tjänst | Beskrivning |
|:---|:---|
| [Azure Automation](/azure/automation/) | Hantera uppdateringar av operativ system och spåra ändringar på Windows-och Linux-datorer. Se [ändringsspårning](../automation/change-tracking.md) och [uppdateringshantering](../automation/automation-update-management.md). |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) | Klassificera och eventuellt skydda dokument och e-postmeddelanden. Se [Central rapportering för Azure information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](/azure/security-center/) | Samla in och analysera säkerhets händelser och utför hot analyser. Se [data insamling i Azure Security Center](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Ansluter till olika källor, inklusive Office 365 och Amazon Web Services Cloud trailer. Se [Anslut data källor](/azure/sentinel/connect-data-sources). |
| [Key Vault-analys](insights/azure-key-vault.md) | Analysera Azure Key Vault AuditEvent-loggar. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Skapa en diagnostisk inställning för att skicka loggar till Azure Monitor. Se [Skicka logg data till lagring, Event Hub eller Log Analytics i Intune (för hands version)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Nätverk  | [Övervakare av nätverksprestanda](insights/network-performance-monitor.md) -övervaka nätverks anslutningar och prestanda till tjänst-och program slut punkter.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) – analysera loggar och mått från Azure Application Gateway.<br>[Trafikanalys](/azure/network-watcher/traffic-analytics) -analyserar Network Watcher nätverks säkerhets grupps flödes loggar (NSG) för att ge insikter i trafikflödet i ditt Azure-moln. |
| [Office 365](insights/solution-office-365.md) | Övervaka din Office 365-miljö. Uppdaterad version med förbättrad onboarding tillgänglig via Azure Sentinel. |
| [SQL-analys](insights/azure-sql.md) | Övervaka prestanda för Azure SQL-databaser, elastiska pooler och hanterade instanser i stor skala och över flera prenumerationer. |
| [Surface Hub](insights/surface-hubs.md) | Spåra hälsan och användningen av Surface Hub enheter. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Samla in data från Operations Manager-agenter genom att ansluta hanterings gruppen till Azure Monitor. Se [ansluta Operations Manager till Azure Monitor](platform/om-agents.md)<br> Utvärdera riskerna och hälsan i din System Center Operations Manager hanterings grupp med [Operations Manager utvärderings](insights/scom-assessment.md) lösning. |
| [Microsoft Teams-rum](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Integrerad hantering av Microsoft Teams-enheter från slut punkt till slut punkt. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Bygg, testa och distribuera program och övervaka sedan deras status och användning. Se [Starta analys av mobilappen med App Center och Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Windows Update kompatibilitet](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) – utvärdera dina Windows Desktop-uppgraderingar.<br>[Skriv bords analys](https://docs.microsoft.com/configmgr/desktop-analytics/overview) – integreras med Configuration Manager för att ge insikt och information för att fatta mer välgrundade beslut om uppdaterings beredskap för dina Windows-klienter. |



## <a name="other-solutions"></a>Andra lösningar
Andra lösningar är tillgängliga för övervakning av olika program och tjänster, men den aktiva utvecklingen har stoppats och är kanske inte tillgänglig i alla regioner. De omfattas av service avtalet för Azure Log Analytics data inmatnings tjänsten.

| Lösning | Beskrivning |
|:---|:---|
| [Active Directory hälso kontroll](insights/ad-assessment.md) | Utvärdera riskerna och hälso tillståndet i dina Active Directorys miljöer. |
| [Active Directory replikeringsstatus](insights/ad-replication-status.md) | Övervakar regelbundet din Active Directorys miljö för eventuella replikeringsfel. |
| [Aktivitets logg analys](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | Analysera aktivitets logg poster med fördefinierade logg frågor och vyer. |
| [DNS-analys (för hands version)](insights/dns-analytics.md) | Samlar in, analyserar och korrelerar Windows DNS analys-och gransknings loggar och andra relaterade data från dina DNS-servrar. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Samla in, Visa och analysera Cloud Foundry systemets hälso tillstånds-och prestanda mått i flera distributioner. |
| [Containrar](insights/containers.md) | Visa och hantera Docker-och Windows container-värdar. |
| [Bedömningar på begäran](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Utvärdera och optimera tillgänglighet, säkerhet och prestanda för dina lokala, hybrid-och molnbaserade Microsoft Technology-miljöer. |
| [SQL-hälsokontroll](insights/sql-assessment.md) | Utvärdera riskerna och hälso tillståndet i dina SQL Servers miljöer.  |
| [Wire Data](insights/wire-data.md) | Sammanställd nätverks-och prestanda data som samlas in från Windows-anslutna och Linux-anslutna datorer med Log Analytics-agenten. |


## <a name="third-party-integration"></a>Integration från tredje part

| Lösning | Beskrivning |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Med Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) kan du ansluta Azure och en ITSM-produkt (IT Service Management) som stöds.  |


## <a name="resources-outside-of-azure"></a>Resurser utanför Azure
Azure Monitor kan samla in data från resurser utanför Azure med hjälp av metoderna som anges i följande tabell.

| Resurs | Metod |
|:---|:---|
| Program | Övervaka webb program utanför Azure med hjälp av Application Insights. Se [Vad är Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Virtuella datorer | Använd Log Analytics-agenten för att samla in data från gäst operativ systemet för virtuella datorer i andra moln miljöer eller lokalt. Se [samla in loggdata med Log Analytics agenten](platform/log-analytics-agent.md). |
| REST API-klient | Separata API: er är tillgängliga för att skriva data till Azure Monitor loggar och mått från valfri REST API-klient. Se [Skicka loggdata till Azure monitor med API: t för HTTP-datainsamling](platform/data-collector-api.md) för loggar och [skicka anpassade mått för en Azure-resurs till Azure Monitor mått lagring med hjälp av en REST API](platform/metrics-store-custom-rest-api.md) för mått. |



## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor data plattform som lagrar loggar och mått som samlas in med insikter och lösningar](platform/data-platform.md).
- Slutför en [själv studie kurs om att övervaka en Azure-resurs](learn/tutorial-resource-logs.md).
- Slutför en [själv studie kurs om hur du skriver en logg fråga för att analysera data i Azure Monitor loggar](learn/tutorial-resource-logs.md).
- Slutför en [själv studie kurs om hur du skapar ett mått diagram för att analysera data i Azure Monitor mått](learn/tutorial-metrics-explorer.md).

 
