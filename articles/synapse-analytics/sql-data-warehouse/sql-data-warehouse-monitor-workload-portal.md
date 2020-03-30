---
title: Övervaka arbetsbelastningen - Azure-portal
description: Övervaka SQL Analytics med Azure-portalen
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 344e2cd03408b5bd1e966abc8abb72bce6078acc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350335"
---
# <a name="monitor-workload---azure-portal"></a>Övervaka arbetsbelastningen - Azure-portal

I den här artikeln beskrivs hur du använder Azure-portalen för att övervaka din arbetsbelastning. Detta inkluderar att konfigurera Azure Monitor Logs för att undersöka frågekörning och arbetsbelastningstrender med hjälp av logganalys för [SQL Analytics](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Krav

- Azure-prenumeration: Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- SQL pool: Vi kommer att samla loggar för en SQL-pool. Om du inte har etablerat en SQL-pool läser du instruktionerna i [Skapa en SQL-pool](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

Navigera till bläddra blad för Log Analytics arbetsytor och skapa en arbetsyta 

![Log Analytics-arbetsytor](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Lägg till Analytics-arbetsyta](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Lägg till Analytics-arbetsyta](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Mer information om arbetsytor finns i följande [dokumentation](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar

Konfigurera diagnostikinställningar för att avge loggar från DIN SQL-pool. Loggar består av telemetrivyer som motsvarar de vanligaste prestandafelsöknings-DVS.Logs consist of telemetry views equivalent to the most commonly used performance troubleshooting DMVs. För närvarande stöds följande vyer:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Aktivera diagnostikloggar](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Loggar kan skickas ut till Azure Storage, Stream Analytics eller Log Analytics. För den här självstudien väljer du Logganalys.

![Ange loggar](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Köra frågor mot Logganalys

Navigera till arbetsytan Log Analytics där du kan göra följande:

- Analysera loggar med hjälp av loggfrågor och spara frågor för återanvändning
- Spara frågor för återanvändning
- Skapa loggaviseringar
- Fästa frågeresultat på en instrumentpanel

Mer information om funktionerna för loggfrågor finns i följande [dokumentation](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Logg Analytics arbetsyta redaktör](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)



![Logganalysarbetsytafrågor](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Exempel på loggfrågor



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

Nu när du har konfigurerat och konfigurerat Azure-övervakningsloggar [kan du anpassa Azure-instrumentpaneler](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) för att dela mellan ditt team.
