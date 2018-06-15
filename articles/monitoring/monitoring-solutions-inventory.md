---
title: Information om samlingen för hanteringslösningar i Azure | Microsoft Docs
description: Lösningar för hantering i Azure är en samling av logik och visualisering data förvärv regler som ger mått pivoteras runt ett specifikt problem.  Den här artikeln innehåller en lista över tillgängliga lösningar från Microsoft och information om deras metod och frekvens för datainsamling.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: bwren
ms.openlocfilehash: ab07a11883b3462c4b9d0f9adab6c55e4fe49d78
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010259"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Information om samlingen för hanteringslösningar i Azure
Den här artikeln innehåller en lista över [hanteringslösningar](monitoring-solutions.md) tillgängliga från Microsoft med länkar till deras detaljerad dokumentation.  Det ger också information på deras metod och frekvens för datainsamling till logganalys.  Du kan använda informationen i den här artikeln för att identifiera de olika lösningarna som är tillgänglig och att förstå de trafikflöde och anslutningen kraven för olika lösningar. 

## <a name="list-of-management-solutions"></a>Lista med lösningar för hantering

Följande tabell listar de [hanteringslösningar](monitoring-solutions.md) i Azure som tillhandahålls av Microsoft. En post i kolumnen innebär att lösningen samlar in data till Log Analytics med hjälp av metoden.  Om en lösning har inga kolumner har valts, sedan skriver den direkt till Log Analytics från en annan Azure-tjänsten. Följ länken för var och en till dess detaljerad dokumentation för mer information.

Beskrivningar av kolumnerna är följande:

- **Microsoft-övervakningsagenten** -agenten används på Windows och Linux för att köra Management pack från SCOM och hanteringslösningar från Azure. I den här konfigurationen är agenten direkt ansluten till logganalys inte är ansluten till en Operations Manager-hanteringsgrupp. 
- **Operations Manager** -identiska agent som Microsoft monitoring agent. I den här konfigurationen har [ansluten till en Operations Manager-hanteringsgrupp](../log-analytics/log-analytics-om-agents.md) som är ansluten till logganalys. 
-  **Azure Storage** -lösningen samlar in data från ett Azure storage-konto. 
- **Operations Manager som krävs?** -En ansluten hanteringsgrupp för Operations Manager krävs för insamling av hanteringslösningen. 
- **Operations Manager agent-data som skickas via hanteringsgruppen** - om agenten är [ansluten till en SCOM-hanteringsgrupp](../log-analytics/log-analytics-om-agents.md), och sedan data skickas till logganalys från hanteringsservern. I det här fallet behöver agenten ansluta direkt till Log Analytics. Om den här rutan inte är markerad sedan skickas data från agenten direkt till Log Analytics även om agenten är ansluten till en SCOM-hanteringsgrupp. antingen måste kunna kommunicera till logganalys via en [OMS gateway](../log-analytics/log-analytics-oms-gateway.md).
- **Samlingen frekvens** – anger hur ofta att data som samlas in av hanteringslösningen. 



| **Lösning** | **Plattform** | **Microsoft övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager som krävs?** | **Operations Manager agent-data som skickas via management-grupp** | **Frekvens för samlingen** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../log-analytics/log-analytics-activity.md) | Azure | | | | | | på meddelande |
| [AD-bedömning](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [AD-replikeringsstatus](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |fem dagar |
| [Agenthälsa](../operations-management-suite/oms-solution-agenthealth.md) | Windows och Linux | &#8226; | &#8226; | | | &#8226; | 1 minut |
| [Varna Management](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |anländer |
| [Varna Management](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 minut |
| [Varna Management](../log-analytics/log-analytics-solution-alert-management.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuter |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Azure | | | | | | Saknas |
| [Application Insights Connector (förhandsgranskning)](../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | på meddelande |
| [Automation Hybrid Worker](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Saknas |
| [Azure Application Gateway Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | på meddelande |
| **Lösning** | **Plattform** | **Microsoft övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager som krävs?** | **Operations Manager agent-data som skickas via management-grupp** | **Frekvens för samlingen** |
| [Azure Nätverkssäkerhetsgruppen Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | på meddelande |
| [Azure SQL Analytics (förhandsgranskning)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1 minut |
| [Säkerhetskopiering](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | på meddelande |
| [Kapacitet och prestanda (förhandsgranskning)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |anländer |
| [Spårning av ändringar](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |varje timme |
| [Spårning av ändringar](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |varje timme |
| [Behållare](../log-analytics/log-analytics-containers.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 3 minuter |
| [Key Vault-analys](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |på meddelande |
| [Utvärdering av skadlig kod](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |varje timme |
| [Övervakning av nätverksprestanda](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP-handskakningar på var femte sekund data skickas var 3: e minut |
| [Office 365 Analytics (förhandsgranskning)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |på meddelande |
| **Lösning** | **Plattform** | **Microsoft övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager som krävs?** | **Operations Manager agent-data som skickas via management-grupp** | **Frekvens för samlingen** |
| [Säkerhet och granska](../operations-management-suite/oms-security-getting-started.md) (Syslog) | Linux | &#8226; | | |  |  | anländer |
| [Säkerhet och granska](../operations-management-suite/oms-security-getting-started.md) (säkerhetshändelseloggar) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | anländer |
| [Säkerhet och granska](../operations-management-suite/oms-security-getting-started.md) (brandväggen loggar) |Windows |&#8226; |&#8226; |  |  |  |anländer |
| [Service Fabric Analytics (förhandsgranskning)](../log-analytics/log-analytics-service-fabric.md) |Windows | | |&#8226; | | |5 minuter |
| [Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 15 sekunder |
| [SQL-bedömning](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |anländer |
| [System Center Operations Manager Assessment (förhandsgranskning)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sju dagar |
| [Hantering av uppdateringar](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |minst 2 gånger per dag och 15 minuter efter installation av en uppdatering |
| [Uppgraderingsberedskap](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dagar |
| [VMware övervakning (förhandsgranskning)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuter |
| [Wire-Data 2.0 (förhandsgranskning)](../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2 / 8.1 eller senare) |&#8226; |&#8226; | | | | 1 minut |




## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa frågor](../log-analytics/log-analytics-log-searches.md) att analysera data som samlas in av lösningar för hantering.
