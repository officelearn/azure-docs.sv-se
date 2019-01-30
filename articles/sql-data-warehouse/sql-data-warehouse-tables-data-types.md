---
title: Definiera datatyper – Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer för att definiera tabelldatatyper i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: aab51c3dc66a1486e8ad7ced55425a2b49c7dea1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247518"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tabelldatatyper i Azure SQL Data Warehouse
Rekommendationer för att definiera tabelldatatyper i Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Vilka är datatyperna som?

SQL Data Warehouse stöder vanligaste de datatyper. En lista över datatyperna som stöds finns i [datatyper](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) i CREATE TABLE-instruktionen. 

## <a name="minimize-row-length"></a>Minimera radlängden
Genom att minimera storleken på datatyper förkortar radlängden, vilket leder till bättre frågeprestanda. Använd den minsta datatypen som fungerar för dina data. 

- Undvik att definiera teckenkolumner med en stor standardlängd. Till exempel om det längsta värdet är 25 tecken definierar du kolumnen som VARCHAR(25). 
- Undvik att använda [NVARCHAR] [NVARCHAR] när du bara behöver VARCHAR.
- Använd om möjligt NVARCHAR(4000) eller VARCHAR(8000) i stället för NVARCHAR(MAX) eller VARCHAR(MAX).

Om du använder PolyBase externa tabeller för att läsa in dina tabeller, får inte den definierade längden för tabellraden överskrida 1 MB. När en rad med variabel längd överskrider 1 MB, kan du läsa in raden med BCP, men inte med PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifiera datatyper
Om du migrerar din databas från en annan SQL-databas, kan du stöta på datatyper som inte stöds i SQL Data Warehouse. Använd den här frågan för att identifiera datatyper i din befintliga SQL-schemat.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Lösningar för datatyper

I följande lista visas datatyperna som SQL Data Warehouse stöder inte och ger alternativ som du kan använda i stället för datatyperna som stöds inte.

| Datatyp | Lösning |
| --- | --- |
| [geometri](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Geografisk plats](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Dela upp kolumn i flera starkt typifierad kolumner. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Konvertera till temporära tabeller. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Omarbeta kod för att använda [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) och [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funktion. Endast konstanter stöds som standard, därför current_timestamp kan inte definieras som en default-begränsning. Om du behöver migrera version radvärden från en kolumn för tidsstämpling skrivna, använder du [binära](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) eller [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) rad Versionsvärden för inte NULL eller NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [användardefinierad typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Konvertera tillbaka till den ursprungliga datatypen när det är möjligt. |
| standardvärden | Standardvärden stöder tidslitteraler och endast konstanter. |


## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns i [Tabellöversikt](sql-data-warehouse-tables-overview.md).
