---
title: Azure SQL Data Warehouse viktig April 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: a9fae37acd653b05502737756baf463188ef631d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861714"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Vad är nytt i Azure SQL Data Warehouse? April 2018
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i April 2018.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Möjligheten att trunkera en Partition innan en växel
Kunder ofta använda partitionsväxling som ett mönster för att läsa in data från en tabell till en annan genom att ändra metadata för tabellen via den `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` syntax. SQL Data Warehouse stöder inte partition växla när målpartitionen innehåller data. Om målpartitionen redan innehåller data, måste kunden att trunkera målpartitionen och sedan köra växeln.

SQL Data Warehouse har nu stöd för den här åtgärden i en enda T-SQL-instruktion.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Mer information finns i den [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) artikeln.

## <a name="improved-query-compilation-performance"></a>Förbättrad frågeprestanda för kompilering
SQL Data Warehouse introducerade en uppsättning ändringar för att förbättra frågesteg för kompilering av distribuerade frågor. De här ändringarna förbättra kompilering frågetider upp till **10 x** minskar den totala fråga körning körningar. Dessa ändringar är tydligare på datalager med ett stort antal objekt (tabeller, funktioner, vyer, procedurer).

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>DBCC-kommandon förbrukar inte Samtidighetsfack (Funktionsändring)
SQL Data Warehouse stöder en delmängd av T-SQL [DBCC-kommandon](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) som [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Tidigare kommandona ska använda en [samtidighet fack](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) minska antalet användare belastningar/frågor som kan utföras. Den `DBCC` kommandon nu körs i en lokal kö som inte använder ett fack för resursen som förbättrar övergripande prestanda för körning av frågor.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Uppdaterade felmeddelande i långa strängar (Funktionsändring)
Tidigare SQL Data Warehouse omfattar en *ungefärliga* räknas när en fråga som finns för många litteraler.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Felmeddelandet har uppdaterats för att visa endast att du har nått den literala gränsen.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Mer information finns i den [frågor](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) delen av den [kapacitetsbegränsningar](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) artiklarna för ytterligare information om maxgränser.

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Ta bort SYS. PDW_DATABASE_MAPPINGS vy (Funktionsändring)
Detta `sys.pdw_database_mappings` vyn kan inte används i SQL Data Warehouse. Tidigare var en väljer i den här vyn skulle inga resultat returneras. Vyn har tagits bort. 

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Stack Overflow-forum]
* [Twitter]


[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
