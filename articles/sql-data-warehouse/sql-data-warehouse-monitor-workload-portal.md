---
title: Övervaka arbetsbelastning – Azure-portalen | Microsoft Docs
description: Övervaka Azure SQL Data Warehouse med Azure portal
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405789"
---
# <a name="monitor-workload---azure-portal"></a>Övervaka arbetsbelastning – Azure-portalen

Den här artikeln beskriver hur du använder Azure-portalen för att övervaka din arbetsbelastning. Detta omfattar att konfigurera Azure Monitor-loggar att undersöka fråga körning och arbetsbelastningen trender med hjälp av log analytics för [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Azure SQL Data Warehouse: Vi kommer samla in loggar för ett SQL data warehouse. Om du inte har ett SQL data warehouse etableras, se anvisningarna i [skapa ett SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

Gå till Bläddra-bladet för Log Analytics-arbetsytor och skapa en arbetsyta 

![Log Analytics-arbetsytor](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Lägga till Analytics-arbetsyta](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Lägga till Analytics-arbetsyta](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Mer information om arbetsytor finns i följande [dokumentation](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar 

Konfigurera diagnostikinställningar för att generera loggar från SQL data warehouse. Loggar består av telemetri vyer för ditt informationslager som motsvarar de vanligaste prestandafelsökning DMV: er för SQL Data Warehouse. För närvarande stöds följande vyer:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Aktiverar diagnostikloggar](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Loggar kan genereras till Azure Storage, Stream Analytics eller Log Analytics. Välj Log Analytics i den här självstudien.

![Ange loggar](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Kör frågor mot Log Analytics

Gå till Log Analytics-arbetsytan där du kan göra följande:

- Analysera loggar med loggfrågor och spara frågor för återanvändning
- Spara frågor för återanvändning
- Skapa aviseringar
- Frågeresultat för PIN-kod på en instrumentpanel

Mer information om funktionerna i loggfrågor, på följande [dokumentation](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Redigeraren för log Analytics-arbetsyta](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Logga Analytics arbetsyta-frågor](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Exempelfrågor för log



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

Nu när du har skapat och konfigurerat Azure monitor-loggar, [anpassa Azure-instrumentpaneler](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) att dela med hela teamet.
