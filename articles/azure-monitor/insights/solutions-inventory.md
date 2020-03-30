---
title: Inventering av övervakningslösningar i Azure | Microsoft-dokument
description: Övervakningslösningar i Azure Monitor är en samling regler för logik, visualisering och datainsamling som ger mått som pivoteras runt ett visst problemområde.  Den här artikeln innehåller en lista över övervakningslösningar som är tillgängliga från Microsoft och information om deras metod och frekvens för datainsamling.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 7b88d957bce45bf518fc77584f1691de8010459a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663138"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Information om lager- och datainsamling för övervakningslösningar i Azure
[Övervakningslösningar](solutions.md) utnyttjar tjänster i Azure för att ge ytterligare insikt i driften av ett visst program eller en viss tjänst. Övervakningslösningar samlar vanligtvis in loggdata och tillhandahåller frågor och vyer för att analysera insamlade data. Du kan lägga till övervakningslösningar i Azure Monitor för alla program och tjänster som du använder. De är vanligtvis tillgängliga utan kostnad men samlar in data som kan anropa användningsavgifter.

Den här artikeln innehåller en lista över [montioring-lösningar](solutions.md) som är tillgängliga från Microsoft med länkar till deras detaljerade dokumentation.  Den innehåller också information om deras metod och frekvens för datainsamling i Azure Monitor.  Du kan använda informationen i den här artikeln för att identifiera de olika lösningar som finns tillgängliga och för att förstå dataflödet och anslutningskraven för olika övervakningslösningar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista över övervakningslösningar

I följande tabell visas [övervakningslösningarna](solutions.md) i Azure som tillhandahålls av Microsoft. En post i kolumnen innebär att lösningen samlar in data i Azure Monitor med den metoden.  Om en lösning inte har några kolumner markerade skriver den direkt till Azure Monitor från en annan Azure-tjänst. Följ länken för var och en till dess detaljerad dokumentation för mer information.

Förklaringar av kolumnerna är följande:

- **Microsofts övervakningsagent** - Agent som används på Windows och Linux för att köra hanteringspaket från SCOM och övervakningslösningar från Azure. I den här konfigurationen är agenten ansluten direkt till Azure Monitor utan att vara ansluten till en Hanteringsgrupp för Operations Manager. 
- **Operations Manager** - Identisk agent som Microsoft-övervakningsagent. I den här konfigurationen är [den ansluten till en Hanteringsgrupp för Operations Manager](../platform/om-agents.md) som är ansluten till Azure Monitor. 
-  **Azure Storage** – Lösningen samlar in data från ett Azure-lagringskonto. 
- **Krävs Operations Manager?** - En ansluten Operations Manager-ledningsgrupp krävs för datainsamling av övervakningslösningen. 
- **Operations Manager-agentdata som skickas via hanteringsgrupp** - Om agenten är [ansluten till en SCOM-hanteringsgrupp](../platform/om-agents.md)skickas data till Azure Monitor från hanteringsservern. I det här fallet behöver agenten inte ansluta direkt till Azure Monitor. Om den här rutan inte är markerad skickas data från agenten direkt till Azure Monitor även om agenten är ansluten till en SCOM-hanteringsgrupp. Den måste kunna kommunicera till Azure Monitor via [Log Analytics-gatewayen](../platform/gateway.md).
- **Insamlingsfrekvens** - Anger hur ofta data samlas in av övervakningslösningen. 



| **Övervakningslösning** | **Plattform** | **Microsofts övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Krävs Operations Manager?** | **Operations Manager-agentdata som skickas via hanteringsgrupp** | **Insamlingsfrekvens** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analys av aktivitetslogg](../platform/activity-log-collect.md) | Azure | | | | | | om anmälan |
| [AD-bedömning](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [AD-replikeringsstatus](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dagar |
| [Agenthälsa](solution-agenthealth.md) | Windows och Linux | &#8226; | &#8226; | | | &#8226; | 1 minut |
| [Alert Management](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |vid ankomsten |
| [Varningshantering](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minut |
| [Varningshantering](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuter |
| [Återställning av Azure-webbplatser](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | Saknas |
| [Application Insights Connector (föråldrad)](../platform/app-insights-connector.md) | Azure | | | |  |  | om anmälan |
| [Hybrid arbetare för automatisering](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Saknas |
| [Azure Application Gateway-analys](azure-networking-analytics.md) | Azure |  |  |  |  |  | om anmälan |
| **Övervakningslösning** | **Plattform** | **Microsofts övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Krävs Operations Manager?** | **Operations Manager-agentdata som skickas via hanteringsgrupp** | **Insamlingsfrekvens** |
| [Azure Network Security Group Analytics (föråldrad)](azure-networking-analytics.md) | Azure |  |  |  |  |  | om anmälan |
| [Azure SQL Analytics (förhandsversion)](azure-sql.md) | Windows | | | | | | 1 minut |
| [Säkerhetskopiering](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | om anmälan |
| [Kapacitet och prestanda (förhandsgranskning)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |vid ankomsten |
| [Spårning av ändringar](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Varierar](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Spårning av ändringar](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Varierar](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Behållare](containers.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 3 minuter |
| [Key Vault-analys](azure-key-vault.md) |Windows | | | | | |om anmälan |
| [Utvärdering av skadlig kod](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |Varje timme |
| [Övervakning av nätverksprestanda](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP-handslag var 5:e sekund, data som skickas var tredje minut |
| [Office 365 Analytics (förhandsversion)](solution-office-365.md) |Windows | | | | | |om anmälan |
| **Övervakningslösning** | **Plattform** | **Microsofts övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Krävs Operations Manager?** | **Operations Manager-agentdata som skickas via hanteringsgrupp** | **Insamlingsfrekvens** |
| [Service Fabric-analys](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minuter |
| [Tjänstkarta](service-map.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 15 sekunder |
| [SQL-bedömning](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [SurfaceHub (SurfaceHub)](surface-hubs.md) |Windows |&#8226; | | | | |vid ankomsten |
| [Utvärdering av System Center Operations Manager (förhandsversion)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sju dagar |
| [Uppdateringshantering](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |minst 2 gånger per dag och 15 minuter efter installation av en uppdatering |
| [Uppgraderingsberedskap](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dagar |
| [VMware-övervakning (föråldrad)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuter |
| [Tråddata 2.0 (förhandsgranskning)](wire-data.md) |Windows (2012 R2 / 8.1 eller senare) |&#8226; |&#8226; | | | | 1 minut |




## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [installerar och använder övervakningslösningar](solutions.md).
* Lär dig hur du [skapar frågor](../log-query/log-query-overview.md) för att analysera data som samlas in av övervakningslösningar.
