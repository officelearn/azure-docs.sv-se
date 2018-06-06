---
title: Azure SQL Data Warehouse viktig information April 2018 | Microsoft Docs
description: Viktig information om Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 49ffc3ddfd586964ae8a9688aeb48fffdd327b45
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738939"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Vad är nytt i Azure SQL Data Warehouse (April 2018)?
Azure SQL Data Warehouse får ständigt förbättringar. Den här artikeln beskrivs nya funktioner och ändringar som har införts i April 2018.

## <a name="features"></a>Funktioner

### <a name="ability-to-truncate-a-partition-before-a-switch"></a>Möjligheten att trunkera en Partition innan en växel
Kunder ofta använder partitionsväxling som ett mönster för att läsa in data från en tabell till en annan genom att ändra metadata för tabellen via den `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` syntax. SQL Data Warehouse stöder inte partition växlar när målpartitionen innehåller data. Om målpartitionen redan innehåller data kan skulle kunden behöva trunkera målpartitionen och utför sedan växeln.

SQL Data Warehouse har nu stöd för den här åtgärden i en enda T-SQL-instruktion.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Mer information finns i [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) artikel.

### <a name="improved-query-compilation-performance"></a>Förbättrad kompilering frågeprestanda
SQL Data Warehouse introducerade en uppsättning ändringar för att förbättra frågesteg för kompilering av distribuerade frågor. De här ändringarna förbättra frågan kompilering gånger upp till **10 x** minskar den totala fråga körning körningar. Är de tydligare på datalager med ett stort antal objekt (tabeller, funktioner, vyer, procedurer).

## <a name="behavior-changes"></a>Funktionsändringar

### <a name="dbcc-commands-do-not-consume-concurrency-slots"></a>DBCC-kommandon upp inte samtidighet fack
SQL Data Warehouse stöder en delmängd av T-SQL [DBCC-kommandon](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) som [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Tidigare kommandona ska använda en [samtidighet fack](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) att minska antalet användare belastningar/frågor som kan utföras. Den `DBCC` kommandon körs nu i en lokal kö som inte använder en resurs plats förbättra prestandan för frågan-körning.

### <a name="updated-error-message-for-excessive-literals"></a>Uppdaterade felmeddelandet överdriven literaler
Tidigare SQL Data Warehouse omfattar en *ungefärliga* räkna när en fråga som finns för många literaler.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Felmeddelandet har uppdaterats för att endast ange att du har nått gränsen för literal.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Mer information finns i [frågor](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) avsnitt i den [kapacitetsbegränsningar](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) artikel för mer information om gränser.

### <a name="removed-the-syspdwdatabasemappings-view"></a>Ta bort SYS. PDW_DATABASE_MAPPINGS vy
Detta `sys.pdw_database_mappings` vyn används inte i SQL Data Warehouse. Tidigare var en väljer i den här vyn skulle inga resultat returneras. Vyn har tagits bort. 