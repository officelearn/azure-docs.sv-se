---
title: Hantera och övervaka arbetsbelastningsprioritet
description: Lär dig hur du hanterar och övervakar prioritet på begäransnivå i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 3efd8a776542616a9ceefba331b06406540905a8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633327"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Hantera och övervaka arbetsbelastningsbetydelse i Azure Synapse Analytics

Hantera och övervaka Synapse SQL-begäransnivå i Azure Synapse med hjälp av DMVs och katalogvyer.

## <a name="monitor-importance"></a>Övervaka betydelse

Övervaka vikten med hjälp av den nya prioritetskolumnen i [den dynamiska hanteringsvyn sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
Övervakningsfrågan nedan visar skicka tid och starttid för frågor. Granska inlämningstiden och starttiden tillsammans med vikten för att se hur viktigt påverkade schemaläggningen.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Om du vill titta närmare på hur frågor schemaläggs använder du katalogvyerna.

## <a name="manage-importance-with-catalog-views"></a>Hantera betydelse med katalogvyer

Katalogvyn sys.workload_management_workload_classifiers innehåller information om klassificerare. Så här utesluter du de systemdefinierade klassificerare som mappas till resursklasser kör följande kod:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Katalogvyn [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)innehåller information om de parametrar som används för att skapa klassificeraren.  Nedanstående fråga visar att ExecReportsClassifier ```membername``` skapades på parametern för värden med ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![frågeresultat](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

För att förenkla felsökningen av felklassificering rekommenderar vi att du tar bort rollmappningar för resursklass när du skapar arbetsbelastningsklassificerare. Koden nedan returnerar befintliga medlemskap i resursklassrollen. Kör sp_droprolemember för varje ```membername``` returnerad från motsvarande resursklass.
Nedan följer ett exempel på att söka efter existens innan du släpper en arbetsbelastningsklassificerare:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Nästa steg

- Mer information om klassificering finns i [Arbetsbelastningsklassificering](sql-data-warehouse-workload-classification.md).
- Mer information om Betydelse finns i [Arbetsbelastningsbetydning](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Gå till Konfigurera arbetsbelastningsbetydning](sql-data-warehouse-how-to-configure-workload-importance.md)
