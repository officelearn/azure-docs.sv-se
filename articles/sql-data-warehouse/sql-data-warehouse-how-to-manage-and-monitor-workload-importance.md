---
title: Hantera och övervaka arbetsbelastningsprioritet
description: Lär dig hur du hanterar och övervakar prioriteten på begärans nivå i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ee9acb873c5118733de142045457028c3f4d5f61
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692704"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Hantera och övervaka arbets belastnings prioritet i Azure SQL Data Warehouse

Hantera och övervaka prioritet för begär ande nivå i Azure SQL Data Warehouse med hjälp av DMV: er och katalogfiler.

## <a name="monitor-importance"></a>Övervaknings prioritet

Övervaka prioritet med kolumnen ny prioritet i vyn för dynamisk hantering i [sys. DM-_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) .
Övervaknings frågan nedan visar sändnings tid och start tid för frågor. Granska sändnings tiden och start tiden tillsammans med viktig information för att se hur viktigt det är att schemalägga.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Använd katalogvyer för att se mer i hur frågor schemaläggs.

## <a name="manage-importance-with-catalog-views"></a>Hantera prioritet med katalogvyer

Catalog-vyn sys. workload_management_workload_classifiers innehåller information om klassificerare i Azure SQL Data Warehouse-instansen. Om du vill utesluta systemdefinierade klassificerare som mappar till resurs klasser kör du följande kod:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Vyn katalog, [sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), innehåller information om de parametrar som används för att skapa klassificeraren.  Frågan nedan visar att ExecReportsClassifier skapades i parametern ```membername``` för värden med ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![frågeresultat](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

För att under lätta fel sökningen av fel klassificering rekommenderar vi att du tar bort mappningar av resurs klass roller när du skapar arbets belastnings klassificerare. Koden nedan returnerar befintliga roll medlemskap i resurs klass. Kör sp_droprolemember för varje ```membername``` som returneras från motsvarande resurs klass.
Nedan visas ett exempel på att söka efter förekomster innan du släpper en arbets belastnings klassificering:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Nästa steg
- Mer information om klassificering finns i avsnittet om [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md).
- Mer information om prioritet finns i [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Gå till konfigurera arbets belastnings prioritet](sql-data-warehouse-how-to-configure-workload-importance.md)
