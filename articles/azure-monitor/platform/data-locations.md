---
title: Övervaka dataplatser i Azure Monitor | Microsoft-dokument
description: Beskriver de olika platser där övervakningsdata lagras i Azure, inklusive Azure Monitor-dataplattformen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666623"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Övervaka dataplatser i Azure Monitor

Azure Monitor baseras på en [dataplattform](data-platform.md) med [loggar](data-platform-logs.md) och mått som [beskrivs](data-platform-metrics.md) i [Azure Monitor-dataplattformen](data-platform.md). Övervakning av data från Azure-resurser kan skrivas till andra platser dock, antingen innan de kopieras till Azure Monitor eller för att stödja ytterligare scenarier. 

## <a name="monitoring-data-locations"></a>Övervaka dataplatser

Följande tabell identifierar de olika platser där övervakningsdata i Azure skickas och de olika metoderna för åtkomst till dem.

| Location | Beskrivning | Metoder för tillgång |
|:---|:---|:---|:--|
| Azure-övervakarmått | Tidsseriedatabas som är optimerad för att analysera tidsstämplade data. | [Metrics Explorer](metrics-getting-started.md)<br>[Api för Azure Monitor-mått](/rest/api/monitor/metrics) |
| Azure Monitor-loggar    | Log Analytics-arbetsyta som baseras på Azure Data Explorer som ger en kraftfull analysmotor och ett omfattande frågespråk. | [Logga Analytics](../log-query/portals.md)<br>[Api för logganalys](https://dev.loganalytics.io/)<br>[API för programinsikter](https://dev.applicationinsights.io/reference/get-query) |
| Aktivitetslogg | Data från aktivitetsloggen är mest användbara när de skickas till Azure Monitor Logs för att analysera dem med andra data, men det samlas också in på egen hand så att den kan visas direkt i Azure-portalen. | [Azure-portal](activity-log-view.md#azure-portal)<br>[Api för Azure Monitor-händelser](/rest/api/monitor/eventcategories) |
| Azure Storage | Vissa datakällor skriver direkt till Azure-lagring och kräver konfiguration för att flytta data till loggar. Du kan också skicka data till Azure-lagring för arkivering och för integrering med externa system.  | [Lagringsanalys](/rest/api/storageservices/storage-analytics)<br>[Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Händelsehubbar | Skicka data till Azure Event Hubs för att strömma dem till andra platser. | [Fånga till lagring](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor för virtuella datorer | Azure Monitor för virtuella datorer lagrar arbetsbelastningshälsodata på en anpassad plats som används av dess övervakningsupplevelse i Azure-portalen. | [Azure-portal](../insights/vminsights-overview.md)<br>[REST-API för arbetsbelastningsövervakare](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[REST-API för Azure Resource-hälsohälsa](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Aviseringar | Aviseringar som skapats av Azure Monitor. | [Azure-portal](alerts-managing-alert-instances.md)<br>[REST-API för hantering av aviseringar](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Nästa steg

- Se de olika källorna [till övervakningsdata som samlats in av Azure Monitor](data-sources.md).
- Lär dig mer om de [typer av övervakningsdata som samlas in av Azure Monitor](data-platform.md) och hur du visar och analyserar dessa data.
