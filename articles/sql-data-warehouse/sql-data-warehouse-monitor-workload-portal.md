---
title: Övervaka arbets belastning – Azure Portal | Microsoft Docs
description: Övervaka Azure SQL Data Warehouse att använda Azure Portal
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 73e7312eacd11fbec052d2b0b7781528c3b3d50f
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575384"
---
# <a name="monitor-workload---azure-portal"></a>Övervaka arbets belastning – Azure Portal

Den här artikeln beskriver hur du använder Azure Portal för att övervaka arbets belastningen. Detta innefattar att ställa in Azure Monitor loggar för att undersöka frågor om körning och arbets belastnings trender med Log Analytics för [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Azure SQL Data Warehouse: Vi kommer att samla in loggar för en SQL Data Warehouse. Om du inte har en SQL Data Warehouse etablerad kan du läsa anvisningarna i [skapa en SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics arbets yta

Navigera till bladet Bläddra för Log Analytics arbets ytor och skapa en arbets yta 

![Log Analytics-arbetsytor](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Lägg till Analytics-arbetsyta](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Lägg till Analytics-arbetsyta](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Mer information om arbets ytor finns i följande [dokumentation](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar 

Konfigurera diagnostikinställningar för att generera loggar från SQL Data Warehouse. Loggar består av telemetri vyer för ditt informations lager som motsvarar de vanligaste prestanda fel söknings DMV: er för SQL Data Warehouse. För närvarande stöds följande vyer:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Aktivera diagnostikloggar](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Loggar kan genereras till Azure Storage, Stream Analytics eller Log Analytics. I den här självstudien väljer du Log Analytics.

![Ange loggar](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Köra frågor mot Log Analytics

Navigera till din Log Analytics arbets yta där du kan göra följande:

- Analysera loggar med hjälp av logg frågor och spara frågor för åter användning
- Spara frågor för åter användning
- Skapa logg aviseringar
- Fäst frågeresultat på en instrument panel

Mer information om funktionerna i logg frågor finns i följande [dokumentation](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Redigerare för Log Analytics arbets yta](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Frågor om Log Analytics-arbetsyta](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Exempel på logg frågor



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat och konfigurerat Azure Monitor-loggar, [anpassar du Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) -instrumentpaneler så att de delas i hela teamet.
