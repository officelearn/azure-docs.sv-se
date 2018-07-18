---
title: Data samling information om lösningar i Azure | Microsoft Docs
description: Lösningar för hantering i Azure är en samling logik, visualisering och datahämtningsregler som tillhandahåller statistik rörande särskilda problemområden.  Den här artikeln innehåller en lista med hanteringslösningar som är tillgängliga från Microsoft och information om deras metod och frekvens för insamling av.
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
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 40b8f51c66ebe98cd1c312002b7bd5e96e5032bd
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112713"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Data samling information om lösningar i Azure
Den här artikeln innehåller en lista över [hanteringslösningar](monitoring-solutions.md) tillgängliga från Microsoft med länkar till deras detaljerad dokumentation.  Det innehåller även information om deras metod och frekvensen för insamling av data till Log Analytics.  Du kan använda informationen i den här artikeln för att identifiera de olika lösningarna som är tillgängliga och förstå datakrav för flödet och anslutningen för olika lösningar. 

## <a name="list-of-management-solutions"></a>Lista över lösningar för hantering

Följande tabell listar de [hanteringslösningar](monitoring-solutions.md) i Azure som tillhandahålls av Microsoft. En post i kolumnen innebär att lösningen samlar in data till Log Analytics med hjälp av metoden.  Om en lösning har inga kolumner har valts, sedan skriver den direkt till Log Analytics från en annan Azure-tjänst. Följ länken för var och en till den detaljerade dokumentationen för mer information.

Förklaringar av kolumnerna är följande:

- **Microsoft övervakningsagent** -Agent som används i Windows och Linux för att köra Management pack från SCOM och hanteringslösningar från Azure. I den här konfigurationen är agenten ansluten direkt till Log Analytics inte är ansluten till en Operations Manager-hanteringsgrupp. 
- **Operations Manager** -identiska agenten som Microsoft monitoring agent. I den här konfigurationen har [är ansluten till en Operations Manager-hanteringsgrupp](../log-analytics/log-analytics-om-agents.md) som är ansluten till Log Analytics. 
-  **Azure Storage** -lösningen samlar in data från ett Azure storage-konto. 
- **Operations Manager som krävs?** – En ansluten hanteringsgrupp för Operations Manager krävs för insamling av hanteringslösningen. 
- **Operations Manager agent-data skickas via hanteringsgruppen** – om agenten [är ansluten till en SCOM-hanteringsgrupp](../log-analytics/log-analytics-om-agents.md), och sedan data skickas till Log Analytics från hanteringsservern. I det här fallet behöver agenten inte att ansluta direkt till Log Analytics. Om den här rutan inte är markerat, sedan skickas data från agenten direkt till Log Analytics även om agenten är ansluten till en SCOM-hanteringsgrupp. det antingen måste kunna kommunicera med Log Analytics genom en [OMS-gatewayen](../log-analytics/log-analytics-oms-gateway.md).
- **Insamlingsfrekvens** – anger hur ofta att data har samlats in av hanteringslösningen. 



| **Lösning för hantering** | **Plattform** | **Microsoft övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager som krävs?** | **Operations Manager agent-data skickas via hanteringsgruppen** | **Insamlingsfrekvens** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../log-analytics/log-analytics-activity.md) | Azure | | | | | | på meddelandet |
| [AD-bedömning](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [AD-replikeringsstatus](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dagar |
| [Agenthälsa](../operations-management-suite/oms-solution-agenthealth.md) | Windows och Linux | &#8226; | &#8226; | | | &#8226; | 1 minut |
| [Avisera Management](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |anländer |
| [Avisera Management](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 minut |
| [Avisera Management](../log-analytics/log-analytics-solution-alert-management.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuter |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Azure | | | | | | Saknas |
| [Application Insights-Anslutningsapp (förhandsversion)](../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | på meddelandet |
| [Automation Hybrid Worker](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Saknas |
| [Azure Application Gateway Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | på meddelandet |
| **Lösning för hantering** | **Plattform** | **Microsoft övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager som krävs?** | **Operations Manager agent-data skickas via hanteringsgruppen** | **Insamlingsfrekvens** |
| [Azure Network Security Group Analytics (inaktuell)](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | på meddelandet |
| [Azure SQL-analys (förhandsversion)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1 minut |
| [Säkerhetskopiering](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | på meddelandet |
| [Kapacitet och prestanda (förhandsgranskning)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |anländer |
| [Spårning av ändringar](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |varje timme |
| [Spårning av ändringar](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |varje timme |
| 
  [Containrar](../log-analytics/log-analytics-containers.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 3 minuter |
| [Key Vault-analys](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |på meddelandet |
| [Utvärdering av skadlig kod](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |varje timme |
| [Övervakning av nätverksprestanda](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP-handskakningar var femte sekund, data skickas var 3: e minut |
| [Office 365 Analytics (förhandsversion)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |på meddelandet |
| **Lösning för hantering** | **Plattform** | **Microsoft övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager som krävs?** | **Operations Manager agent-data skickas via hanteringsgruppen** | **Insamlingsfrekvens** |
| [Service Fabric-analys (förhandsversion)](../log-analytics/log-analytics-service-fabric.md) |Windows | | |&#8226; | | |5 minuter |
| [Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 15 sekunder |
| [SQL-bedömning](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |anländer |
| [System Center Operations Manager-utvärdering (förhandsversion)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sju dagar |
| [Hantering av uppdateringar](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |minst 2 gånger per dag och 15 minuter när du har installerat en uppdatering |
| [Uppgraderingsberedskap](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dagar |
| [VMware Monitoring (inaktuell)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuter |
| [Wire Data 2.0 (förhandsversion)](../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2 / 8.1 eller senare) |&#8226; |&#8226; | | | | 1 minut |




## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa frågor](../log-analytics/log-analytics-log-searches.md) att analysera data som samlas in av lösningar för hantering.
