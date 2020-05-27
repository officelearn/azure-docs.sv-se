---
title: Inventering av övervaknings lösningar i Azure | Microsoft Docs
description: Övervaknings lösningar i Azure Monitor är en samling logik-, visualiserings-och data hämtnings regler som tillhandahåller mått som pivoteras runt ett visst problemområden.  Den här artikeln innehåller en lista över övervaknings lösningar som är tillgängliga från Microsoft och information om deras metod och frekvens för data insamling.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: b87179c79489bf781619b70b19ca8982f2e38dff
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816817"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Inventerings-och data insamlings information för övervakning av lösningar i Azure
[Övervaknings lösningar](solutions.md) använder tjänster i Azure för att få kunskap om driften av ett visst program eller en viss tjänst. Övervaknings lösningar samlar normalt in loggdata och tillhandahåller frågor och vyer för att analysera insamlade data. Du kan lägga till övervaknings lösningar som Azure Monitor för alla program och tjänster som du använder. De är vanligt vis tillgängliga utan kostnad utan att samla in data som kan anropa användnings kostnader.

Den här artikeln innehåller en lista över [övervaknings lösningar](solutions.md) som är tillgängliga från Microsoft med länkar till detaljerad dokumentation.  Den innehåller också information om deras metod och frekvens för data insamling till Azure Monitor.  Du kan använda informationen i den här artikeln för att identifiera de olika tillgängliga lösningarna och förstå data flödet och anslutnings kraven för olika övervaknings lösningar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista över övervaknings lösningar

I följande tabell visas de [övervaknings lösningar](solutions.md) i Azure som tillhandahålls av Microsoft. En post i kolumnen innebär att lösningen samlar in data i Azure Monitor använder den metoden.  Om en lösning inte har valt några kolumner skriver den direkt till Azure Monitor från en annan Azure-tjänst. Följ länken för var och en av de detaljerade dokumentationen för mer information.

Förklaringarna av kolumnerna är följande:

- **Microsoft Monitoring Agent** – agent som används i Windows och Linux för att köra hanterings paket från Microsoft System Center – Operations Manager (om) och övervaknings lösningar från Azure. I den här konfigurationen är agenten direkt ansluten till Azure Monitor utan att vara ansluten till en Operations Manager hanterings grupp. 
- **Operations Manager** -identisk agent som Microsoft Monitoring Agent. I den här konfigurationen är den [ansluten till en Operations Manager hanterings grupp](../platform/om-agents.md) som är ansluten till Azure Monitor. 
-  **Azure Storage** -lösning samlar in data från ett Azure Storage-konto. 
- **Operations Manager krävs.** – En ansluten Operations Manager hanterings grupp krävs för data insamling av övervaknings lösningen. 
- **Operations Manager agent data som skickas via hanterings grupp** – om agenten är [ansluten till en om-hanterings grupp](../platform/om-agents.md)skickas data till Azure Monitor från hanterings servern. I det här fallet behöver agenten inte ansluta direkt till Azure Monitor. Om den här rutan inte är markerad skickas data från agenten direkt till Azure Monitor även om agenten är ansluten till en OM-hanterings grupp. Den måste kunna kommunicera med Azure Monitor via [Log Analytics Gateway](../platform/gateway.md).
- **Samlings frekvens** – anger den frekvens som data samlas in av övervaknings lösningen. 



| **Övervaknings lösning** | **Plattform** | **Microsoft Monitoring Agent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager krävs.** | **Operations Manager agent data som skickas via hanterings gruppen** | **Insamlingsfrekvens** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Aktivitets logg analys](../platform/activity-log-collect.md) | Azure | | | | | | vid meddelande |
| [AD-bedömning](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [AD-replikeringsstatus](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dagar |
| [Agenthälsa](solution-agenthealth.md) | Windows och Linux | &#8226; | &#8226; | | | &#8226; | 1 minut |
| [Aviseringshantering](../platform/alert-management-solution.md) (nagios) |Linux |&#8226; | | | | |vid ankomsten |
| [Aviseringshantering](../platform/alert-management-solution.md) (zabbix) |Linux |&#8226; | | | | |1 minut |
| [Aviseringshantering](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuter |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | saknas |
| [Application Insights-anslutningsprogram (inaktuell)](../platform/app-insights-connector.md) | Azure | | | |  |  | vid meddelande |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | saknas |
| [Azure Application Gateway-analys](azure-networking-analytics.md) | Azure |  |  |  |  |  | vid meddelande |
| **Övervaknings lösning** | **Plattform** | **Microsoft Monitoring Agent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager krävs.** | **Operations Manager agent data som skickas via hanterings gruppen** | **Insamlingsfrekvens** |
| [Azure-nätverkssäkerhetsgruppanalys (inaktuell)](azure-networking-analytics.md) | Azure |  |  |  |  |  | vid meddelande |
| [Azure SQL-analys (för hands version)](azure-sql.md) | Windows | | | | | | 1 minut |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | vid meddelande |
| [Kapacitet och prestanda (för hands version)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |vid ankomsten |
| [Spårning av ändringar](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[sig](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Spårning av ändringar](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[sig](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Containrar](containers.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 3 minuter |
| [Key Vault-analys](azure-key-vault.md) |Windows | | | | | |vid meddelande |
| [Utvärdering av skadlig kod](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |per timme |
| [Övervakning av nätverksprestanda](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP-handskakning var femte sekund, data skickas var 3: e minut |
| [Office 365-analys (för hands version)](solution-office-365.md) |Windows | | | | | |vid meddelande |
| **Övervaknings lösning** | **Plattform** | **Microsoft Monitoring Agent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager krävs.** | **Operations Manager agent data som skickas via hanterings gruppen** | **Insamlingsfrekvens** |
| [Service Fabric-analys](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minuter |
| [Tjänstkarta](service-map.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 15 sekunder |
| [SQL-bedömning](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |vid ankomsten |
| [System Center Operations Manager-utvärdering (för hands version)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sju dagar |
| [Hantering av uppdateringar](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |minst två gånger per dag och 15 minuter efter installation av en uppdatering |
| [Uppgraderingsberedskap](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dagar |
| [VMware-övervakning (inaktuell)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuter |
| [Wire Data 2.0 (för hands version)](wire-data.md) |Windows (2012 R2/8,1 eller senare) |&#8226; |&#8226; | | | | 1 minut |




## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [installerar och använder övervaknings lösningar](solutions.md).
* Lär dig hur du [skapar frågor](../log-query/log-query-overview.md) för att analysera data som samlas in av övervaknings lösningar.
