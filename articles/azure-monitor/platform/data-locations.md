---
title: Övervaka data platser i Azure Monitor | Microsoft Docs
description: Beskriver de olika platser där övervaknings data lagras i Azure, inklusive Azure Monitor data plattform.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666623"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Övervaka data platser i Azure Monitor

Azure Monitor baseras på en [data plattform](data-platform.md) med [loggar](data-platform-logs.md) och [mått](data-platform-metrics.md) enligt beskrivningen i [Azure Monitor data plattform](data-platform.md). Övervakning av data från Azure-resurser kan skrivas till andra platser, antingen innan de kopieras till Azure Monitor eller för att stödja ytterligare scenarier. 

## <a name="monitoring-data-locations"></a>Övervaka data platser

I följande tabell visas de olika platser där övervaknings data i Azure skickas och de olika metoderna för att få åtkomst till den.

| plats. | Beskrivning | Åtkomst metoder |
|:---|:---|:---|:--|
| Azure Monitor mått | Databas för tids serier som är optimerad för att analysera tidsstämplade data. | [Metrics Explorer](metrics-getting-started.md)<br>[Azure Monitor Metrics-API](/rest/api/monitor/metrics) |
| Azure Monitor-loggar    | Log Analytics arbets yta som baseras på Azure Datautforskaren som tillhandahåller en kraftfull analys motor och ett avancerat frågespråk. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics-API](https://dev.loganalytics.io/)<br>[Application Insights-API](https://dev.applicationinsights.io/reference/get-query) |
| Aktivitetslogg | Data från aktivitets loggen är mest användbara när de skickas till Azure Monitor loggar för att analysera den med andra data, men den samlas också in på egen hand, så att den kan visas direkt i Azure Portal. | [Azure Portal](activity-log-view.md#azure-portal)<br>[API för Azure Monitor händelser](/rest/api/monitor/eventcategories) |
| Azure Storage | Vissa data källor skrivs direkt till Azure Storage och kräver konfiguration för att flytta data till loggar. Du kan också skicka data till Azure Storage för arkivering och för integrering med externa system.  | [Lagringsanalys](/rest/api/storageservices/storage-analytics)<br>[Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Skicka data till Azure Event Hubs för att strömma dem till andra platser. | [Avbilda till lagring](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor för virtuella datorer | Azure Monitor for VMs lagrar hälso data för arbets belastning på en anpassad plats som används av dess övervaknings upplevelse i Azure Portal. | [Azure Portal](../insights/vminsights-overview.md)<br>[REST API arbets belastnings övervakare](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource Health-REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Aviseringar | Aviseringar som skapats av Azure Monitor. | [Azure Portal](alerts-managing-alert-instances.md)<br>[REST API för aviserings hantering](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Nästa steg

- Se de olika källorna till [övervaknings data som samlas in av Azure Monitor](data-sources.md).
- Lär dig mer om vilka [typer av övervaknings data som samlas in av Azure Monitor](data-platform.md) och hur du visar och analyserar dessa data.
