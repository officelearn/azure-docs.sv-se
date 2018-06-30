---
title: Lägg till Azure logganalys hanteringslösningar | Microsoft Docs
description: Lösningar för hantering i Azure är en samling av logik och visualisering data förvärv regler som ger mått pivoteras runt ett specifikt problem.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 0cffdbcaa128dce52758e4b618b93dd763961361
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131483"
---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>Lägg till Azure logganalys hanteringslösningar i arbetsytan

Hanteringslösningar för log Analytics är en samling **logik**, **visualiseringen**, och **data förvärv regler** som ger mått pivoteras runt en viss problemet område. Den här artikeln visar hur du lägger till och ta bort för en arbetsyta med hjälp av Azure portal finns hanteringslösningar som stöds av logganalys.

Hanteringslösningar Tillåt djupare insikter om:

* Att undersöka och lösa operativa problem snabbare
* Samla in och korrelera olika typer av data för datorer
* Hjälper dig att proaktivt med aktiviteter som exponerar lösningen.

> [!NOTE]
> Logganalys innehåller logg-sökning, så du inte behöver installera en lösning för att aktivera den. Men får du datavisualiseringar, föreslagna sökningar och insikter genom att lägga till hanteringslösningar din arbetsyta.

Med den här artikeln kan du lägga till hanteringslösningar till en arbetsyta med hjälp av Azure portal Marketplace. När du har lagt till en lösning data samlas in från servrar i din infrastruktur och skickas till logganalys. Bearbetning vanligtvis tar några minuter till en timme. När tjänsten bearbetar data, kan du visa den i logganalys.

Du kan enkelt ta bort en hanteringslösning när den inte längre behövs. När du tar bort en lösning för skickas dess data inte till logganalys. Om du är på den kostnadsfria prisnivån kan tar bort en lösning minska mängden data som används kan hjälpa dig under dagliga kvoten av data.

## <a name="view-available-management-solutions"></a>Visa tillgängliga lösningar

På Azure marketplace innehåller listan över [hanteringslösningar för Log Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Du kan installera hanteringslösningar från Azure marketplace genom att klicka på den **blir det nu** länken längst ned i varje lösning.

## <a name="add-a-management-solution"></a>Lägg till en lösning
1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com) med din Azure-prenumeration.
2. Välj **skapar du en resurs** > **övervakning + Management** > **se alla**.  
    ![Övervaka + bladet hantering](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. Till höger om **hanteringslösningar**, klickar du på **mer**.
5. I den **hanteringslösningar** bladet Välj en hanteringslösning som du vill lägga till en arbetsyta.  
    ![Övervaka + bladet hantering](./media/log-analytics-add-solutions/management-solutions.png)  
6. Granska information om hanteringslösningen i bladet management-lösningen och klicka sedan på **skapa**.
7. På bladet *hanteringslösningsnamn* väljer du en arbetsyta som du vill koppla till hanteringslösningen.
8. Du kan också ändra arbetsytan inställningar för Azure-prenumeration, resursgrupp och plats. Du kan också välja **automatiseringsalternativ**. Klicka på **Skapa**.  
    ![lösningens arbetsyta](./media/log-analytics-add-solutions/solution-workspace.png)  
9. Om du vill börja använda den hanteringslösning som du har lagt till din arbetsyta, gå till **logganalys** > **prenumerationer** > ***Arbetsytenamn***  >  **Översikt**. En ny panel som din lösning för visas. Klicka på panelen om du vill öppna den och börja använda lösningen data för lösningen har samlats in.

## <a name="remove-a-management-solution"></a>Ta bort en lösning

1. I den [Azure-portalen](https://portal.azure.com), gå till **logganalys** > **prenumerationer** > ***Arbetsytenamn*** och i den ***Arbetsytenamn*** bladet, klickar du på **lösningar**.
2. Välj den lösning som du vill ta bort i listan med hanteringslösningar.
3. I bladet lösning för din arbetsyta klickar du på **ta bort**.  
    ![ta bort lösning](./media/log-analytics-add-solutions/solution-delete.png)  
4. I dialogrutan klickar du på **Ja**.

## <a name="offers-and-pricing-tiers"></a>Erbjudanden och prisnivåer

I följande tabell beskrivs vilka lösningar som hör till varje Operations Management Suite-erbjudandet.
Tabellen identifierar också prisnivåer som är tillgängliga för varje lösning.
Alla lösningar i följande tabell är tillgängliga i Azure-portalen och lösningsgalleriet i logganalys-portalen.

| Lösning                                                                       | Erbjudande                                                                     | Prisnivåer<sup>1</sup>                                                 | Anteckningar |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [Activity Log Analytics](log-analytics-activity.md)                                                                   | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | 90 dagars data är tillgängliga gratis<br>Data som inte omfattas av den kostnadsfria nivån fjärrskrivbordsanslutning |
| [AD-bedömning](log-analytics-ad-assessment.md)                                           | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [AD-replikeringsstatus](log-analytics-ad-replication-status.md)                           | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | Inte tillgängligt för att lägga till från Azure portal/marketplace. |
| [Agenthälsa](../operations-management-suite/oms-solution-agenthealth.md)                                                                                | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | Data som inte omfattas av den kostnadsfria nivån fjärrskrivbordsanslutning<br> Inte tillgängligt för att lägga till från Azure portal/marketplace. |
| [Varningshantering](log-analytics-solution-alert-management.md)                            | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | Inte tillgängligt för att lägga till från Azure portal/marketplace. |
| [Application Insights Connector (förhandsgranskning)](log-analytics-app-insights-connector.md)                                               | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Automation Hybrid Worker](../automation/automation-hybrid-runbook-worker.md)                                                                     | <ul><li>Automatisering och kontroll</li></ul>                                  | Kostnadsfri<br> Per&nbsp;nod&nbsp;(OMS)                                                                         | Kräver logganalys-arbetsytan ska kopplas till ett Automation-konto |
| [Azure Application Gateway Analytics](log-analytics-azure-networking-analytics.md)    | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Azure Nätverkssäkerhetsgruppen Analytics](log-analytics-azure-networking-analytics.md)     | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Azure SQL Analytics (förhandsgranskning)](log-analytics-azure-sql.md)                                                       | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br>Per&nbsp;nod&nbsp;(OMS)                                                                          | Kräver logganalys-arbetsytan ska kopplas till ett Automation-konto|
| [Azure Web Apps-analys](log-analytics-azure-web-apps-analytics.md)     | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
|[Säkerhetskopiering](../backup/backup-introduction-to-azure-backup.md)                                                                                 | <ul><li>Insikter och analys</li></ul>                                   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)                                                                       | Kräver en klassiska Backup-valvet.<br> Inte tillgängligt för att lägga till från Azure portal/marketplace. |
| [Kapacitet och prestanda (förhandsgranskning)](log-analytics-capacity.md)                                                   | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Spårning av ändringar](log-analytics-change-tracking.md)                                       | <ul><li>Automatisering och kontroll</li></ul>                                  | Kostnadsfri<br> Per&nbsp;nod&nbsp;(OMS)                                                                         | Kräver logganalys-arbetsytan ska kopplas till ett Automation-konto |
| [Behållare](log-analytics-containers.md)                                                 | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [IT Service Management Connector](log-analytics-itsmc-overview.md)                                                | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Per&nbsp;nod&nbsp;(OMS)     | |
| HDInsight HBase övervakning <br>(Förhandsversion)                                                  | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Key Vault-analys](log-analytics-azure-key-vault.md)                   | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Logic Apps B2B](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)                    | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | Inte tillgängligt för att lägga till från Azure portal/marketplace. |
| [Utvärdering av skadlig kod](log-analytics-malware.md)                                            | <ul><li>Säkerhet och efterlevnad</li></ul>                                 | Kostnadsfri<br> Fristående<br>Per&nbsp;nod&nbsp;(OMS)                                                                           | Om du lägger till säkerhet och efterlevnad lösningar efter 19 juni 2017 [faktureringen är per nod](https://azure.microsoft.com/pricing/details/security-compliance/), oavsett arbetsytan prisnivån. De första 60 dagarna är kostnadsfri.  |
| [Övervakning av nätverksprestanda](log-analytics-network-performance-monitor.md) <br>  | <ul><li>Insikter och analys</li></ul>                                   | Kostnadsfri<br> Per&nbsp;nod&nbsp;(OMS)                                                                         | |
| [Office 365 Analytics (förhandsgranskning)](../operations-management-suite/oms-solution-office-365.md)                                                       | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Säkerhet och granskning](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>Säkerhet&nbsp;och&nbsp;kompatibilitet</li></ul>                       | Kostnadsfri<br> Fristående<br>Per&nbsp;nod&nbsp;(OMS)                                                                           | Samla in säkerhetshändelser kräver den här lösningen<br>Om du lägger till säkerhet och efterlevnad lösningar efter 19 juni 2017 [faktureringen är per nod](https://azure.microsoft.com/pricing/details/security-compliance/), oavsett arbetsytan prisnivån. De första 60 dagarna är kostnadsfri. |
| [Service Fabric Analytics (förhandsgranskning)](log-analytics-service-fabric.md)                     | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Tjänstmappning (förhandsgranskning)](../operations-management-suite/operations-management-suite-service-map.md) | <ul><li>Insikter och analys</li></ul>                      | Kostnadsfri<br> Per&nbsp;nod&nbsp;(OMS)                                                                         | Tillgängligt i östra USA, västra Europa och West centrala USA    |
| [Site Recovery](../site-recovery/site-recovery-overview.md)                                                                               | <ul><li>Insikter och analys</li></ul>                                   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)                                                                       | Kräver en klassiska Site Recovery-valvet.<br> Inte tillgängligt för att lägga till från Azure portal/marketplace. |
| [SQL-bedömning](log-analytics-sql-assessment.md)                                         | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| Starta/stoppa virtuella datorer utanför arbetstid<br>(Förhandsversion)                                              | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Per&nbsp;nod&nbsp;(OMS)                                                                         | Kräver logganalys-arbetsytan ska kopplas till ett Automation-konto |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | Inte tillgängligt för att lägga till från Azure portal/marketplace. |
| [System Center Operations Manager Assessment (förhandsgranskning)](log-analytics-scom-assessment.md)  | <ul><li>Insikter och analys</li><li>Log Analytics</li></ul>        | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Hantering av uppdateringar](../operations-management-suite/oms-solution-update-management.md)                                                                         | <ul><li>Automatisering och kontroll</li></ul>                                  | Kostnadsfri<br> Per&nbsp;nod&nbsp;(OMS)                                                                         | Kräver logganalys-arbetsytan ska kopplas till ett Automation-konto |
| [Uppdateringsefterlevnad (förhandsgranskning)](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)                                                             | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | Utan kostnad för data eller noder<br>Data som inte omfattas av den kostnadsfria nivån fjärrskrivbordsanslutning.<br> Inte tillgängligt för att lägga till från Azure portal/marketplace. |
| [Uppgraderingsberedskap](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started)                                                          | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | Utan kostnad för data eller noder<br>Data som inte omfattas av den kostnadsfria nivån fjärrskrivbordsanslutning.<br> Inte tillgängligt för att lägga till från Azure portal/marketplace. |
| [VMware övervakning (förhandsgranskning)](log-analytics-vmware.md)                                | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Standard<br> Premium&nbsp;(OMS)<br> Per&nbsp;GB&nbsp;(fristående)<br> Per&nbsp;nod&nbsp;(OMS)   | |
| [Wire-Data 2.0 (förhandsgranskning)](log-analytics-wire-data.md)                                                                 | <ul><li>Insikter och analys</li></ul>                                   | Kostnadsfri<br> Per&nbsp;nod&nbsp;(OMS)                                                                         | Tillgängligt i östra USA, västra Europa och West centrala USA |

<sup>1</sup> den *Standard* och *Premium (OMS)* prisnivåer är bara tillgängliga för kunder som skapade sina logganalys-arbetsytan innan den 21 September 2016.

### <a name="community-provided-management-solutions"></a>Gemenskapen tillhandahålls lösningar för hantering

Gemenskapen tillhandahålls lösningar som är tillgängliga från den [Azure mallgalleriet](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS)) och direkt från författarna.

| Lösning               | Erbjudande                                                                     | Prisnivåer                         | Anteckningar |
| ---                               | ---                                                                       | ---                                   | ---   |
| Alla community tillhandahålls lösningar  | <ul><li>Insight&nbsp;och&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Kostnadsfri<br> Per&nbsp;nod&nbsp;(OMS)     |   Kräver logganalys-arbetsytan ska kopplas till ett Automation-konto |




## <a name="data-collection-details"></a>Information om samlingen
Följande tabeller visar data collection metoder och annan information om hur data samlas in för Log Analytics hanteringslösningar och datakällor. Tabellerna kategoriseras efter lösning erbjudanden som motsvarar [prenumeration prisnivåer](https://go.microsoft.com/fwlink/?linkid=827926). Aktiviteten logganalys-lösning är tillgänglig för alla prisnivåer kostnadsfritt.

Log Analytics Windows-agenten och System Center Operations Manager-agenten är i princip samma. Windows-agenten innehåller ytterligare funktioner för att tillåta att ansluta till logganalys-arbetsytan och vidarebefordra via en proxyserver. Om du använder en Operations Manager-agenten måste vara mål som en OMS-agenten kan kommunicera med logganalys. Operations Manager-agenter i den här tabellen är OMS-agenter som är anslutna till Operations Manager. Se [ansluta Operations Manager till logganalys](log-analytics-om-agents.md) information om hur du ansluter din befintliga Operations Manager-miljö till logganalys.

> [!NOTE]
> Agent som du använder bestämmer hur data skickas till logganalys, med följande villkor:
> - Du använda antingen Windows-agenten eller en Operations Manager-anslutna OMS-agent.
> - När Operations Manager krävs, skickas Operations Manager agent-data för lösningen alltid till Log Analytics med hjälp av Operations Manager-hanteringsgruppen. Dessutom när Operations Manager krävs används endast Operations Manager-agenten av lösningen.
> - När Operations Manager krävs inte och visas som Operations Manager agent-data skickas till logganalys med hanteringsgruppen och sedan Operations Manager agent data skickas alltid till logganalys med hanteringsgrupper. Windows-agenter kringgå hanteringsgruppen och skicka data direkt till Log Analytics.
> - När data för Operations Manager-agenten inte skickas med hjälp av en hanteringsgrupp, är data skickas direkt till Log Analytics – kringgå hanteringsgruppen.

### <a name="insight--analytics--log-analytics"></a>Insight & Analytics / Log Analytics

| Lösning | Plattform | Microsoft övervakningsagent | Operations Manager-agent | Azure-lagring | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Aktivitetslogganalys | Azure |   |   |   |   |   | på meddelande |
| AD-bedömning |Windows |&#8226; |&#8226; |  |  |&#8226; |7 dagar |
| AD-replikeringsstatus |Windows |&#8226; |&#8226; |  |  |&#8226; |fem dagar |
| Agenthälsa | Windows och Linux | &#8226; | &#8226; |   |   | &#8226; | 1 minut |
| Alert Management (Nagios) |Linux |&#8226; |  |  |  |  |anländer |
| Alert Management (Zabbix) |Linux |&#8226; |  |  |  |  |1 minut |
| Alert Management (Operations Manager) |Windows |  |&#8226; |  |&#8226; |&#8226; |3 minuter |
| Application Insights-anslutningsapp (förhandsversion) | Azure |   |   |   |   |   | på meddelande |
| Azure Application Gateway Analytics | Azure |   |   |   |   |   | på meddelande |
| Azure Network Security Group Analytics | Azure |   |   |   |   |   | på meddelande |
| Azure SQL Analytics (förhandsversion) |Windows |  |  |  |  |  | 10 minuter |
| Kapacitetshantering |Windows |&#8226; |&#8226; |  |  |&#8226; |anländer |
| Behållare | Windows och Linux | &#8226; | &#8226; |   |   |   | 3 minuter |
| Key Vault-analys |Windows |  |  |  |  |  |på meddelande |
| Övervakning av nätverksprestanda | Windows | &#8226; | &#8226; |   |   |   | TCP-handskakningar på var femte sekund data skickas var 3: e minut |
| Office 365 Analytics (förhandsversion) |Windows |  |  |  |  |  |på meddelande |
| Service Fabric-analys |Windows |  |  |&#8226; |  |  |5 minuter |
| Tjänstkarta | Windows och Linux | &#8226; | &#8226; |   |   |   | 15 sekunder |
| SQL-bedömning |Windows |&#8226; |&#8226; |  |  |&#8226; |7 dagar |
| SurfaceHub |Windows |&#8226; |  |  |  |  |anländer |
| System Center Operations Manager Assessment (förhandsgranskning) | Windows | &#8226; | &#8226; |   |   | &#8226; | sju dagar |
| Uppgraderingsanalys (förhandsversion) | Windows | &#8226; |   |   |   |   | 2 dagar |
| VMware Monitoring (förhandsversion) | Linux | &#8226; |   |   |   |   | 3 minuter |
| Wire Data |Windows (2012 R2 / 8.1 eller senare) |&#8226; |&#8226; |  |  |  | 1 minut |


### <a name="automation--control"></a>Automatisering och kontroll

| Lösning | Plattform | Microsoft övervakningsagent | Operations Manager-agent | Azure-lagring | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Automation Hybrid Worker | Windows | &#8226; | &#8226; |   |   |   | Saknas |
| Spårning av ändringar |Windows |&#8226; |&#8226; |  |  |&#8226; |varje timme |
| Spårning av ändringar |Linux |&#8226; |  |  |  |  |varje timme |
| Uppdateringshantering | Windows |&#8226; |&#8226; |  |  |&#8226; |minst 2 gånger per dag och 15 minuter efter installation av en uppdatering |

### <a name="security--compliance"></a>Säkerhet och efterlevnad

| Lösning | Plattform | Microsoft övervakningsagent | Operations Manager-agent | Azure-lagring | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Utvärdering av program mot skadlig kod |Windows |&#8226; |&#8226; |  |  |&#8226; |varje timme |
| Säkerhet och granska<sup>1</sup> | Windows och Linux | partiell | partiell | partiell |   | partiell | olika |

<sup>1</sup> på säkerhet och granska lösningen kan samla in loggar från Windows, Operations Manager och Linux-agenter. Se [datakällor](#data-sources) data samling information om:

- Syslog
- Händelseloggarna för Windows-säkerhet
- Windows-brandväggen-loggar
- Windows-händelseloggar



### <a name="protection--recovery"></a>Skydd och återställning

| Lösning | Plattform | Microsoft övervakningsagent | Operations Manager-agent | Azure-lagring | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | Azure |   |   |   |   |   | Saknas |
| Azure Site Recovery | Azure |   |   |   |   |   | Saknas |


### <a name="data-sources"></a>Datakällor


| Datakälla | Plattform | Microsoft övervakningsagent | Operations Manager-agent | Azure-lagring | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Azure aktivitetsloggar |Windows |  |  |  |  |  |på meddelande |
| Azure diagnostikloggar |Windows |  |  |  |  |  |på meddelande |
| Azure diagnostisk mått |Windows |  |  |  |  |  |på meddelande |
| ETW |Windows |  |  |&#8226; |  |  |5 minuter |
| IIS-loggar |Windows |&#8226; |&#8226; |&#8226; |  |  |5 minuter |
| Prestandaräknare |Windows |&#8226; |&#8226; |  |  |  |som schemalagts minst 10 sekunder |
| Prestandaräknare |Linux |&#8226; |  |  |  |  |som schemalagts minst 10 sekunder |
| Syslog |Linux |&#8226; |  |  |  |  |från Azure storage: 10 minuter. från agent: anländer |
| Händelseloggarna för Windows-säkerhet |Windows |&#8226; |&#8226; |&#8226; |  |  |för Azure storage: 10 min; för agenten: anländer |
| Windows-brandväggen-loggar |Windows |&#8226; |&#8226; |  |  |  |anländer |
| Windows-händelseloggar |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; |för Azure storage: 10 min; för agenten: anländer |



## <a name="preview-management-solutions-and-features"></a>Förhandsgranska hanteringslösningar och funktioner
Genom att köra en tjänst och följa devops-metoder, kommer du att samarbeta med kunder att utveckla funktioner och lösningar.

Under privata förhandsgranskningen skickar vi en liten grupp av kunder åtkomst till en tidig implementering av funktionen eller lösningen för att få feedback och göra förbättringar. Den här tidig implementeringen har minimal funktioner som fungerar.

Vårt mål är att snabbt prova saker så att vi kan hitta vad som fungerar och vad fungerar inte. Vi gå igenom den här processen tills feedback från privat förhandsversion kunder informerar oss om att vi är redo för en förhandsversion.

Den offentliga förhandsversionen tillgängliggöra vi funktioner eller lösningar för alla användare att få mer feedback och verifiera vår skalning och effektivitet. Under den här fasen:

* Förhandsgranskningsfunktioner visas på fliken Inställningar och kan aktiveras av någon användare.
* Förhandsgranska lösningar läggs igenom galleriet eller använda ett skript.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>Vad bör jag veta om funktioner och lösningar?
Vi gärna berättar om nya funktioner till och lösningar för hantering och vi arbetar med att utveckla dem.

Är inte rätt för alla funktioner och lösningar. Innan du ber att ansluta till en privat förhandsversion eller aktivera en förhandsversion Kontrollera arbetar OK du med något som är under utveckling.

När du aktiverar en förhandsvisningsfunktion via portalen kan du se en varning påminna dig om att funktionen är i förhandsgranskningen.

#### <a name="for-both-private-and-public-preview"></a>För både *privata* och *offentliga* förhandsgranskning
Följande information gäller för både offentliga och privata förhandsversioner:

* Saker fungerar kanske inte alltid.
  * Skickar intervall från att vara en mindre illustrerar via till något inte fungerar alls.
* Det finns en risk för att förhandsgranskningen ska ha en negativ inverkan på dina system / miljö.
  * Vi försöker undvika negativa saker händer till system som du använder men ibland oväntat saker.
* Dataförlust / kan skadas.
* Vi be dig att samla in diagnostikloggar eller andra data för att felsöka problem.
* Funktionen eller lösning kan tas bort (tillfälligt eller permanent).
  * Baserat på vår learnings under förhandsgranskningen vi besluta att inte viktig funktion eller lösning.
* Förhandsgranskningar fungerar inte eller kan inte har testats med alla konfigurationer och vi kan begränsa:
  * Operativsystem som kan användas (t.ex, en funktion kan endast gäller för Linux i preview).
  * Typ av agent (MMA, Operations Manager) som kan användas (t.ex, en funktion kanske inte fungerar med Operations Manager i preview).  
* Preview lösningar och funktioner som omfattas inte av serviceavtal.
* Användning av förhandsgranskningsfunktioner debiteras användning.
* Funktioner eller möjligheter som behövs för funktionen / lösningen ska vara användbar kanske saknas eller är ofullständig.
* Funktioner / lösningar kanske inte tillgänglig i alla regioner.
* Funktioner / lösningar inte är lokaliserade.
* Funktioner / lösningar har kanske en gräns för antalet kunder eller enheter som kan använda den.
* Du kan behöva använda skript för att konfigurera och aktivera lösning/funktionen.
* Användargränssnittet (UI) ändras från dagliga är ofullständig.
* Offentliga förhandsgranskningar kanske inte är lämpliga för din produktion / kritiska system.

#### <a name="for-private-preview"></a>För *privata* förhandsgranskning
Förutom punkterna ovan är specifik för privat förhandsgranskning följande information:

* Vi hoppas att du kan ge oss feedback om din upplevelse så att vi kan göra funktionen/lösningen bättre.
* Vi kan kontakta dig för feedback med undersökningar, telefonsamtal eller e-post.
* Saker fungerar alltid inte.
* Vi kan kräva en icke-avslöjande av avtalet (Sekretessavtalet) för deltagande eller kan omfatta konfidentiellt innehåll.
  * Innan du bloggar, Twitter eller på annat sätt att kommunicera med tredje part, kontrollera med Programhanteraren som ansvarar för att förhandsgranska för att förstå begränsningar av avslöjande.
* Kör inte produktion / kritiska system.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Hur får åtkomst till privat förhandsversion funktioner och lösningar?
Vi erbjuder kunder att privat förhandsgranskning via flera olika sätt beroende på förhandsgranskningen.

* Svara på kundens månatliga undersökningen och ger oss åtkomstbehörighet till uppföljning förbättrar risken för som bjudits in till en privat förhandsgranskning.
* Du kan välja att Microsoft-kontoteamet.
* Du kan registrera dig baserat på information på twitter [msopsmgmt](https://twitter.com/msopsmgmt).
* Du kan registrera dig baserat på information om delade community händelser – leta efter oss på uppfyller ups konferenser och online-communities.

## <a name="next-steps"></a>Nästa steg
* [Söka i loggar](log-analytics-log-searches.md) att visa detaljerad information som samlas in av lösningar för hantering.
