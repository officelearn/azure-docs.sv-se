---
title: Hantera och övervaka arbetsbelastningen betydelse i Azure SQL Data Warehouse | Microsoft Docs
description: Lär dig mer om att hantera och övervaka begäran på vikten.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: a39d71e0f8b8072cab6a3af9a2f0913973f303ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235933"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Hantera och övervaka arbetsbelastningen betydelse i Azure SQL Data Warehouse

Hantera och övervaka begäran på betydelse i Azure SQL Data Warehouse med hjälp av DMV: er och katalogvyer.

## <a name="monitor-importance"></a>Övervaka prioritet

Övervaka vikten med hjälp av den nya vikt-kolumnen i den [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) dynamisk hanteringsvy.
Den under övervakning frågan visar skicka tid och starttid för frågor. Granska skicka tid och starttid tillsammans med vikten för att se hur vikten påverkas schemaläggning.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Om du vill se ytterligare över hur frågor som schemat, använda katalogvyerna.

## <a name="manage-importance-with-catalog-views"></a>Hantera vikten med katalogvyer

Katalogvy sys.workload_management_workload_classifiers innehåller information om klassificerare i din Azure SQL Data Warehouse-instans. Om du vill exkludera kör systemdefinierade klassificerare som mappar till resursklasser du följande kod:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Katalogvy, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), innehåller information om de parametrar som används av klassificeraren.  Den nedan fråga visas som ExecReportsClassifier skapades på den ```membername``` parametern för värden med ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Frågeresultat](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

För att förenkla felsökning felklassificering, rekommenderar vi att du tar bort resursen klassmappningar rollen när du skapar arbetsbelastning klassificerare. Koden nedan returnerar befintlig resurs rollmedlemskap för klassen. Kör sp_droprolemember för var och en ```membername``` returnerades från motsvarande resursklass.
Nedan visas ett exempel på kontrollerar om finns innan du släpper en klassificerare arbetsbelastning:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Nästa steg
- Mer information om klassificering finns i [arbetsbelastning klassificering](sql-data-warehouse-workload-classification.md).
- Läs mer på vikten [arbetsbelastning prioritet](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Gå till Konfigurera vikten för arbetsbelastning ](sql-data-warehouse-how-to-configure-workload-importance.md)
