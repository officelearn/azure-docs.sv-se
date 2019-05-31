---
title: Övervakning av data platser i Azure Monitor | Microsoft Docs
description: Beskriver de olika platser där övervakning av data lagras i Azure, inklusive Azure Monitor-dataplattform.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416909"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Övervakning av data platser i Azure Monitor

Azure Monitor är baserad på en [dataplattform](data-platform.md) av [loggar](data-platform-logs.md) och [mått](data-platform-metrics.md) enligt beskrivningen i [Azure Monitor dataplattform](data-platform.md). Övervakning av data från Azure-resurser kan skrivas till andra platser men antingen innan de kopieras till Azure Monitor eller för att ge stöd för ytterligare scenarier. 

## <a name="monitoring-data-locations"></a>Övervakning av data platser

I följande tabell visas de olika platser där övervakning av data i Azure skickas och de olika metoderna för att komma åt den.

| Location | Beskrivning | Åtkomstmetoder |
|:---|:---|:---|:--|
| Azure Monitor-mått | Time series-databas som är optimerad för att analysera data med tidsstämpel. | [Metrics Explorer](metrics-getting-started.md)<br>[Azure Monitor mått-API](/rest/api/monitor/metrics) |
| Azure Monitor-loggar    | Log Analytics-arbetsyta som baseras på Azure Data Explorer som ger en kraftfull analys-motor och omfattande frågespråk. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics-API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| Aktivitetslogg | Data från aktivitetsloggen är mest användbara när de skickas till Azure Monitor-loggar och analysera dem med andra data, men den har också samlats in på egen hand så att den kan direkt visas i Azure-portalen. | [Azure Portal](activity-log-view.md#azure-portal)<br>[Azure Monitor händelser API](/rest/api/monitor/eventcategories) |
| Azure Storage | Vissa datakällor skriver direkt till Azure storage och måste konfigureras för att flytta data till loggarna. Du kan också skicka data till Azure-lagring för arkivering och för integrering med externa system.  | [Lagringsanalys](/rest/api/storageservices/storage-analytics)<br>[Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Skicka data till Azure Event Hubs för att strömma det till andra platser. | [Avbildning till Storage](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor för virtuella datorer | Azure Monitor för virtuella datorer lagras hälsodata för arbetsbelastning på en annan plats som används av dess övervakningsupplevelse i Azure-portalen. | [Azure Portal](../insights/vminsights-overview.md)<br>[Övervaka arbetsbelastning REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource health REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Aviseringar | Aviseringar som skapats av Azure Monitor. | [Azure Portal](alerts-managing-alert-instances.md)<br>[Hantering av REST-API aviseringar](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Nästa steg

- Se de olika källorna av [övervakningsdata som samlas in av Azure Monitor](data-sources.md).
- Lär dig mer om den [typerna av övervakningsdata som samlas in av Azure Monitor](data-platform.md) och hur du visar och analyserar dessa data.
