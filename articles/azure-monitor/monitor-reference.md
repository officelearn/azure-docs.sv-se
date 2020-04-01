---
title: Vad övervakas av Azure Monitor
description: Referens för alla tjänster och andra resurser som övervakas av Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 0ef7e5d869ab2d7e085cbf861bfc32e57b1fad4b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408496"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Vad övervakas av Azure Monitor?
I den här artikeln beskrivs de olika program och tjänster som övervakas av Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Insikter och kärnlösningar
Grundläggande insikter och lösningar betraktas som en del av Azure Monitor och följer support- och servicenivåavtalen för Azure. De stöds i alla Azure-regioner där Azure Monitor är tillgängligt.

### <a name="insights"></a>Insikter

Insikter ger en anpassad övervakningsupplevelse för särskilda program och tjänster. De samlar in och analyserar både loggar och mått.

| Insight | Beskrivning |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | APM-tjänst (Extensible Application Performance Management) för att övervaka ditt live-webbprogram på valfri plattform. |
| [Azure Monitor för behållare](insights/container-insights-overview.md) | Övervakar prestanda för behållararbetsbelastningar som distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). |
| [Azure Monitor för Cosmos DB (förhandsversion)](insights/cosmosdb-insights-overview.md) | Ger en vy över den övergripande prestanda, fel, kapacitet och driftshälsa för alla dina Azure Cosmos DB-resurser i en enhetlig interaktiv upplevelse. |
| [Azure Monitor för nätverk (förhandsversion)](insights/network-insights-overview.md) | Ger en omfattande översikt över hälso- och mått för alla nätverksresurser. Den avancerade sökfunktionen hjälper dig att identifiera resursberoenden, vilket möjliggör scenarier som att identifiera resurser som är värd för din webbplats genom att helt enkelt söka efter ditt webbplatsnamn. |
[Azure Monitor för resursgrupper (förhandsgranskning)](insights/resource-group-insights.md) |  Triage och diagnostisera eventuella problem dina individuella resurser möter, samtidigt som sammanhang om hälsa och prestanda för resursgruppen som helhet. |
| [Azure Monitor för lagring (förhandsversion)](insights/storage-insights-overview.md) | Ger omfattande övervakning av dina Azure Storage-konton genom att leverera en enhetlig vy över dina Azure Storage-tjänsters prestanda, kapacitet och tillgänglighet. |
| [Azure Monitor för virtuella datorer (förhandsgranskning)](insights/container-insights-overview.md) | Övervakar dina Azure-virtuella datorer (VM) och skaluppsättningar för virtuella datorer i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer. |

### <a name="core-solutions"></a>Kärnlösningar

Lösningar baseras på loggfrågor och vyer som anpassats för ett visst program eller en viss tjänst. De samlar in och analysera loggar bara och håller på att inaktuella över tiden till förmån för insikter.

| Lösning | Beskrivning |
|:---|:---|
| [Agent hälsa](insights/solution-agenthealth.md) | Analysera hälsotillståndet och konfigurationen av Log Analytics-agenter. |
| [Varningshantering](platform/alert-management-solution.md) | Analysera varningar som samlats in från System Center Operations Manager, Nagios eller Zabbix. |
| [Tjänstkarta](insights/service-map.md) | Identifierar automatiskt programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster. |



## <a name="azure-services"></a>Azure-tjänster
I följande tabell visas Azure-tjänster och de data som de samlar in i Azure Monitor. 

- Mått - Tjänsten samlar automatiskt in mått i Azure Monitor Metrics. 
- Loggar - Tjänsten stöder diagnostikinställningar som kan samla in plattformsloggar och mått till Azure Monitor Logs.
- Insikt - Det finns en insikt tillgänglig för tjänsten som ger en anpassad övervakningsupplevelse för tjänsten.

| Tjänst | Mått | Loggar | Insight | Anteckningar |
|:---|:---|:---|:---|:---|
|Active Directory | Inga | Ja | [Ja](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Inga | Inga | Inga |  |
|Active Directory Domain Services | Inga | Ja | Inga |  |
|Aktivitetslogg | Inga | Ja | Inga | |
|Advanced Threat Protection | Inga | Inga | Inga |  |
|Advisor | Inga | Inga | Inga |  |
|AI Builder | Inga | Inga | Inga |  |
|Analysis Services | Ja | Ja | Inga |  |
|API för FHIR | Inga | Inga | Inga |  |
|API Management | Ja | Ja | Inga |  |
|App Service | Ja | Ja | Inga |  |
|AppConfig | Inga | Inga | Inga |  |
|Application Gateway | Ja | Ja | Inga |  |
|Attesteringstjänst | Inga | Inga | Inga |  |
|Automation | Ja | Ja | Inga |  |
|Azure Service Manager (RDFE) | Inga | Inga | Inga |  |
|Backup | Inga | Ja | Inga |  |
|Bastion | Inga | Inga | Inga |  |
|Batch | Ja | Ja | Inga |  |
|Batch AI | Inga | Inga | Inga |  |
|Blockchain Service | Inga | Ja | Inga |  |
|Skisser | Inga | Inga | Inga |  |
|Robottjänst | Inga | Inga | Inga |  |
|Cloud Services | Ja | Ja | Inga | Agenten måste övervaka gästoperativsystem och arbetsflöden.  |
|Cloud Shell | Inga | Inga | Inga |  |
|Cognitive Services | Ja | Ja | Inga |  |
|Container Instances | Ja | Inga | Inga |  |
|Container Registry | Ja | Ja | Inga |  |
|Content Delivery Network (CDN) | Inga | Ja | Inga |  |
|Cosmos DB | Ja | Ja | [Ja](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Inga | Inga | Inga |  |
|Data Box | Inga | Inga | Inga |  |
|Gen2 för datakatalog2 | Inga | Inga | Inga |  |
|Data Explorer | Ja | Ja | Inga |  |
|Data Factory | Ja | Ja | Inga |  |
|Data Factory v2 | Inga | Ja | Inga |  |
|Data Share | Inga | Inga | Inga |  |
|Database for MariaDB | Ja | Ja | Inga |  |
|Database for MySQL | Ja | Ja | Inga |  |
|Database for PostgreSQL | Ja | Ja | Inga |  |
|Database Migration Service | Inga | Inga | Inga |  |
|Databricks | Inga | Ja | Inga |  |
|DDoS Protection | Ja | Ja | Inga |  |
|DevOps | Inga | Inga | Inga |  |
|DNS | Ja | Inga | Inga |  |
|Domännamn | Inga | Inga | Inga |  |
|Dps | Inga | Inga | Inga |  |
|Dynamics 365 kundengagemang | Inga | Inga | Inga |  |
|Dynamics 365 Ekonomi och verksamhet | Inga | Inga | Inga |  |
|Event Grid | Ja | Inga | Inga |  |
|Event Hubs | Ja | Ja | Inga |  |
|ExpressRoute | Ja | Ja | Inga |  |
|Brandvägg | Ja | Ja | Inga |  |
|Front Door | Ja | Ja | Inga |  |
|Functions | Ja | Ja | Inga |  |
|HDInsight | Inga | Ja | Inga |  |
|HPC Cache | Inga | Inga | Inga |  |
|Informationsskydd | Inga | Ja | Inga |  |
|Intune | Inga | Ja | Inga |  |
|IoT Central | Inga | Inga | Inga |  |
|IoT Hub | Ja | Ja | Inga |  |
|Key Vault | Ja | Ja | Inga |  |
|Kubernetes Service (AKS) | Inga | Inga | [Ja](insights/container-insights-overview.md)  |  |
|Lastbalanserare | Ja | Ja | Inga |  |
|Logic Apps | Ja | Ja | Inga |  |
|Machine Learning Service | Inga | Inga | Inga |  |
|Managed Applications  | Inga | Inga | Inga |  |
|Kartor  | Inga | Inga | Inga |  |
|Media Services | Ja | Ja | Inga |  |
|Microsoft Flow | Inga | Inga | Inga |  |
|Microsoft Hanterat skrivbord | Inga | Inga | Inga |  |
|Microsoft PowerApps | Inga | Inga | Inga |  |
|Microsoft Social Engagement | Inga | Inga | Inga |  |
|Microsoft Stream | Ja | Ja | Inga |  |
|Migrera | Inga | Inga | Inga |  |
|Multi-Factor Authentication | Inga | Ja | Inga |  |
|Network Watcher | Ja | Ja | Inga |  |
|Notification Hubs | Ja | Inga | Inga |  |
|Open Datasets | Inga | Inga | Inga |  |
|Princip | Inga | Inga | Inga |  |
|Power BI | Ja | Ja | Inga |  |
|Power BI Embedded | Inga | Inga | Inga |  |
|Private Link | Inga | Inga | Inga |  |
|Kommunikationsplattform för projektspole | Inga | Inga | Inga |  |
|Red Hat OpenShift | Inga | Inga | Inga |  |
|Redis Cache | Ja | Ja | Inga |  |
|Resursdiagram | Inga | Inga | Inga |  |
|Resource Manager | Inga | Inga | Inga |  |
|Retail Search – genom Bing | Inga | Inga | Inga |  |
|Search | Ja | Ja | Inga |  |
|Service Bus | Ja | Ja | Inga |  |
|Service Fabric | Inga | Ja | Inga | Agenten måste övervaka gästoperativsystem och arbetsflöden.  |
|Registreringsportal | Inga | Inga | Inga |  |
|Site Recovery | Inga | Ja | Inga |  |
|Molntjänst för våren | Inga | Inga | Inga |  |
|SQL Data Warehouse | Ja | Ja | Inga |  |
|SQL Database | Ja | Ja | Inga |  |
|SQL Server Stretch Database | Ja | Ja | Inga |  |
|Stack | Inga | Inga | Inga |  |
|Storage | Ja | Inga | [Ja](insights/storage-insights-overview.md) |  |
|Cache för lagring | Inga | Inga | Inga |  |
|Synkroniseringstjänster för lagring | Inga | Inga | Inga |  |
|Stream Analytics | Ja | Ja | Inga |  |
|Time Series Insights | Ja | Ja | Inga |  |
|Tina | Inga | Inga | Inga |  |
|Traffic Manager | Ja | Ja | Inga |  |
|Universell utskrift | Inga | Inga | Inga |  |
|Virtual Machine Scale Sets | Inga | Ja | [Ja](insights/vminsights-overview.md) | Agenten måste övervaka gästoperativsystem och arbetsflöden. |
|Virtuella datorer | Ja | Ja | [Ja](insights/vminsights-overview.md) | Agenten måste övervaka gästoperativsystem och arbetsflöden. |
|Virtual Network | Ja | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtuellt nätverk - NSG-flödesloggar | Inga | Ja | Inga |  |
|VPN Gateway | Ja | Ja | Inga |  |
|Windows Virtual Desktop | Inga | Inga | Inga |  |


## <a name="product-integrations"></a>Produktintegrationer
Tjänsterna och lösningarna i följande tabell lagrar sina data på en Log Analytics-arbetsyta så att de kan analyseras med andra loggdata som samlas in av Azure Monitor.

| Produkt/tjänst | Beskrivning |
|:---|:---|
| [Azure Automation](/azure/automation/) | Hantera uppdateringar av operativsystemet och spåra ändringar på Windows- och Linux-datorer. Se [Ändringsspårning](../automation/change-tracking.md) [och uppdateringshantering](../automation/automation-update-management.md). |
| [Skydd av Azure-information](https://docs.microsoft.com/azure/information-protection/) | Klassificera och eventuellt skydda dokument och e-postmeddelanden. Se [Central rapportering för Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](/azure/security-center/) | Samla in och analysera säkerhetshändelser och utföra hotanalys. Se [Datainsamling i Azure Security Center](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Ansluter till olika källor, inklusive Office 365 och Amazon Web Services Cloud Trail. Se [Ansluta datakällor](/azure/sentinel/connect-data-sources). |
| [Key Vault-analys](insights/azure-key-vault.md) | Analysera Azure Key Vault AuditEvent-loggar. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Skapa en diagnostikinställning för att skicka loggar till Azure Monitor. Se [Skicka loggdata till lagring, händelsehubbar eller logganalys i Intune (förhandsversion).](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)  |
| Nätverk  | [Network Performance Monitor](insights/network-performance-monitor.md) - Övervaka nätverksanslutning och prestanda till tjänst- och programslutpunkter.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - Analysera loggar och mått från Azure Application Gateway.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) - Analyserar NSG-flödesloggar (Network Watcher Network Security Group) för att ge insikter om trafikflödet i ditt Azure-moln. |
| [Office 365](insights/solution-office-365.md) | Övervaka din Office 365-miljö. Uppdaterad version med förbättrad introduktion tillgänglig via Azure Sentinel. |
| [SQL-analys](insights/azure-sql.md) | Övervaka prestanda för Azure SQL-databaser, elastiska pooler och hanterade instanser i stor skala och över flera prenumerationer. |
| [Surface Hub](insights/surface-hubs.md) | Spåra hälsotillstånd och användning av Surface Hub-enheter. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Samla in data från Operations Manager-agenter genom att ansluta deras hanteringsgrupp till Azure Monitor. Se [Ansluta Operations Manager till Azure Monitor](platform/om-agents.md)<br> Utvärdera risken och hälsan för din Hanteringsgrupp för System Center Operations Manager med [Operation Manager Assessment-lösning.](insights/scom-assessment.md) |
| [Microsoft Teams-rum](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Integrerad, heltäckande hantering av Microsoft Teams Rooms-enheter. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Skapa, testa och distribuera program och övervaka sedan deras status och användning. Se [Börja analysera din mobilapp med App Center och Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Windows Update Compliance](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) – Utvärdera uppgraderingar av Skrivbordet i Windows.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) - Integreras med Configuration Manager för att ge insikt och intelligens för att fatta mer välgrundade beslut om uppdateringsberedskap för dina Windows-klienter. |



## <a name="other-solutions"></a>Andra lösningar
Andra lösningar finns tillgängliga för övervakning av olika program och tjänster, men aktiv utveckling har stoppats och de kanske inte är tillgängliga i alla regioner. De omfattas av Azure Log Analytics-avtalet om datainmatningstjänstnivå.

| Lösning | Beskrivning |
|:---|:---|
| [Hälsokontroll av Active Directory](insights/ad-assessment.md) | Utvärdera risken och hälsan för dina Active Directory-miljöer. |
| [Status för Active Directory-replikering](insights/ad-replication-status.md) | Övervakar regelbundet Active Directory-miljön för eventuella replikeringsfel. |
| [Analys av aktivitetslogg](platform/activity-log-view.md#azure-portal) | Visa aktivitetsloggposter. |
| [DNS Analytics (förhandsgranskning)](insights/dns-analytics.md) | Samlar in, analyserar och korrelerar Windows DNS-analys- och granskningsloggar och andra relaterade data från DINA DNS-servrar. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Samla in, visa och analysera hälso- och prestandamått för cloud foundry-systemet över flera distributioner. |
| [Behållare](insights/containers.md) | Visa och hantera Docker- och Windows-behållarvärdar. |
| [Bedömningar på begäran](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Utvärdera och optimera tillgängligheten, säkerheten och prestandan för dina lokala, hybrid- och molnteknikmiljöer för Microsoft. |
| [Hälsokontroll av SQL](insights/sql-assessment.md) | Utvärdera risken och hälsan för dina SQL Server-miljöer.  |
| [Wire Data](insights/wire-data.md) | Konsoliderade nätverks- och prestandadata som samlats in från Datorer som är anslutna till Windows och Linux-anslutna med Log Analytics-agenten. |


## <a name="third-party-integration"></a>Integration av tredje part

| Lösning | Beskrivning |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Med ITSMC (IT Service Management Connector) kan du ansluta Azure och en ITSM-produkt/tjänst (SOM stöds av IT Service Management).  |


## <a name="resources-outside-of-azure"></a>Resurser utanför Azure
Azure Monitor kan samla in data från resurser utanför Azure med hjälp av de metoder som anges i följande tabell.

| Resurs | Metod |
|:---|:---|
| Program | Övervaka webbprogram utanför Azure med application insights. Se [Vad är Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Virtuella datorer | Använd Log Analytics-agenten för att samla in data från gästoperativsystemet för virtuella datorer i andra molnmiljöer eller lokalt. Se [Samla in loggdata med Log Analytics-agenten](platform/log-analytics-agent.md). |
| REST API-klient | Separata API:er är tillgängliga för att skriva data till Azure Monitor Loggar och mått från alla REST API-klienter. Se [Skicka loggdata till Azure Monitor med HTTP Data Collector API](platform/data-collector-api.md) för loggar och skicka anpassade mått för en [Azure-resurs till Azure Monitor-måttarkivet med hjälp av ett REST API](platform/metrics-store-custom-rest-api.md) för mått. |



## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor-dataplattformen som lagrar loggar och mått som samlas in av insikter och lösningar](platform/data-platform.md).
- Slutför en [självstudiekurs om övervakning av en Azure-resurs](learn/tutorial-resource-logs.md).
- Slutför en [självstudiekurs om hur du skriver en loggfråga för att analysera data i Azure Monitor Logs](learn/tutorial-resource-logs.md).
- Slutför en [självstudiekurs om hur du skapar ett måttdiagram för att analysera data i Azure Monitor Metrics](learn/tutorial-metrics-explorer.md).

 
