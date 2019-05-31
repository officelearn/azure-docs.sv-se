---
title: Inventering av övervakning lösningar i Azure | Microsoft Docs
description: Övervakar lösningar i Azure Monitor är en samling logik, visualisering och datahämtningsregler som tillhandahåller statistik rörande särskilda problemområden.  Den här artikeln innehåller en lista över tillgängliga från Microsoft och information om deras metod och frekvens för insamling av övervakningslösningar.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 9398815ea75c0eacd99a6e40c569254fac671cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234022"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Inventering och data samling information för att övervaka lösningar i Azure
[Övervakningslösningar](solutions.md) utnyttja tjänster i Azure för att ge ytterligare insikter i driften av ett visst program eller tjänst. Övervakningslösningar normalt samlar in loggdata och Tillhandahåll frågor och vyer för att analysera insamlade data. Du kan lägga till övervakningslösningar Azure Monitor för alla program och tjänster som du använder. De finns vanligtvis på utan kostnad men samla in data som kan anropa avgifter för användning.

Den här artikeln innehåller en lista över [montioring lösningar](solutions.md) tillgängliga från Microsoft med länkar till deras detaljerad dokumentation.  Det innehåller även information om deras metod och frekvensen för insamling av data i Azure Monitor.  Du kan använda informationen i den här artikeln för att identifiera de olika lösningarna som är tillgängliga och förstå datakrav för flödet och anslutningen för olika lösningar för övervakning.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista över övervakningslösningar

Följande tabell listar de [övervakningslösningar](solutions.md) i Azure som tillhandahålls av Microsoft. En post i kolumnen innebär att lösningen samlar in data i Azure Monitor med hjälp av metoden.  Om en lösning har inga kolumner har valts, sedan skriver den direkt till Azure Monitor från en annan Azure-tjänst. Följ länken för var och en till den detaljerade dokumentationen för mer information.

Förklaringar av kolumnerna är följande:

- **Microsoft övervakningsagent** - agenten används på Windows och Linux för att köra Management pack från SCOM och övervakningslösningar från Azure. I den här konfigurationen är agenten ansluten direkt till Azure Monitor inte är ansluten till en Operations Manager-hanteringsgrupp. 
- **Operations Manager** -identiska agenten som Microsoft monitoring agent. I den här konfigurationen har [är ansluten till en Operations Manager-hanteringsgrupp](../platform/om-agents.md) som är ansluten till Azure Monitor. 
-  **Azure Storage** -lösningen samlar in data från ett Azure storage-konto. 
- **Operations Manager som krävs?** – En ansluten hanteringsgrupp för Operations Manager krävs för insamling av lösningen för fjärrövervakning. 
- **Operations Manager agent-data skickas via hanteringsgruppen** – om agenten [är ansluten till en SCOM-hanteringsgrupp](../platform/om-agents.md), och sedan data skickas till Azure Monitor från hanteringsservern. I det här fallet behöver agenten inte att ansluta direkt till Azure Monitor. Om den här rutan inte är markerat, sedan skickas data från agenten direkt till Azure Monitor även om agenten är ansluten till en SCOM-hanteringsgrupp. Den måste kunna kommunicera med Azure Monitor via den [Log Analytics gateway](../platform/gateway.md).
- **Insamlingsfrekvens** -anger med vilken frekvens som data samlas in av lösningen för fjärrövervakning. 



| **Lösningen för fjärrövervakning** | **Plattform** | **Microsoft övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager som krävs?** | **Operations Manager agent-data skickas via hanteringsgruppen** | **Insamlingsfrekvens** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Aktivitetslogganalys](../platform/activity-log-collect.md) | Azure | | | | | | på meddelandet |
| [AD-bedömning](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [AD-replikeringsstatus](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dagar |
| [Agenthälsa](solution-agenthealth.md) | Windows och Linux | &#8226; | &#8226; | | | &#8226; | 1 minut |
| [Avisera Management](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |anländer |
| [Avisera Management](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minut |
| [Avisera Management](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuter |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | Saknas |
| [Application Insights-Anslutningsapp (inaktuell)](../platform/app-insights-connector.md) | Azure | | | |  |  | på meddelandet |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Saknas |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | på meddelandet |
| **Lösningen för fjärrövervakning** | **Plattform** | **Microsoft övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager som krävs?** | **Operations Manager agent-data skickas via hanteringsgruppen** | **Insamlingsfrekvens** |
| [Azure Network Security Group Analytics (inaktuell)](azure-networking-analytics.md) | Azure |  |  |  |  |  | på meddelandet |
| [Azure SQL-analys (förhandsversion)](azure-sql.md) | Windows | | | | | | 1 minut |
| [Säkerhetskopiering](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | på meddelandet |
| [Kapacitet och prestanda (förhandsgranskning)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |anländer |
| [Spårning av ändringar](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Varierar](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Spårning av ändringar](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Varierar](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Containrar](containers.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 3 minuter |
| [Key Vault-analys](azure-key-vault.md) |Windows | | | | | |på meddelandet |
| [Utvärdering av skadlig kod](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |varje timme |
| [Övervakning av nätverksprestanda](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP-handskakningar var femte sekund, data skickas var 3: e minut |
| [Office 365 Analytics (förhandsversion)](solution-office-365.md) |Windows | | | | | |på meddelandet |
| **Lösningen för fjärrövervakning** | **Plattform** | **Microsoft övervakningsagent** | **Operations Manager-agent** | **Azure Storage** | **Operations Manager som krävs?** | **Operations Manager agent-data skickas via hanteringsgruppen** | **Insamlingsfrekvens** |
| [Service Fabric-analys](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minuter |
| [Tjänstkarta](service-map.md) | Windows och Linux | &#8226; | &#8226; |  |  |  | 15 sekunder |
| [SQL-bedömning](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dagar |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |anländer |
| [System Center Operations Manager-utvärdering (förhandsversion)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sju dagar |
| [Hantering av uppdateringar](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |minst 2 gånger per dag och 15 minuter när du har installerat en uppdatering |
| [Uppgraderingsberedskap](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dagar |
| [VMware Monitoring (Deprecated)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuter |
| [Wire Data 2.0 (förhandsversion)](wire-data.md) |Windows (2012 R2 / 8.1 eller senare) |&#8226; |&#8226; | | | | 1 minut |




## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [installera och använda övervakningslösningar](solutions.md).
* Lär dig hur du [skapa frågor](../log-query/log-query-overview.md) att analysera data som samlas in genom att övervaka lösningar.
