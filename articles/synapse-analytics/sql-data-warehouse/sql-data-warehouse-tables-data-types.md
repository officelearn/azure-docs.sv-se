---
title: Definiera datatyper
description: Rekommendationer för att definiera tabelldatatyper i Azure SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 020b6fc08dad0dacb51215eca6f7baf127a9dba6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351321"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tabelldatatyper i Azure SQL Data Warehouse
Rekommendationer för att definiera tabelldatatyper i Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Vilka är datatyperna?

SQL Data Warehouse stöder de vanligaste datatyperna. En lista över datatyper som stöds finns i [datatyper](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) i CREATE TABLE-satsen. 

## <a name="minimize-row-length"></a>Minimera radlängd
Om du minimerar storleken på datatyper förkortas radlängden, vilket leder till bättre frågeprestanda. Använd den minsta datatyp som fungerar för dina data. 

- Undvik att definiera teckenkolumner med en stor standardlängd. Om till exempel det längsta värdet är 25 tecken definierar du kolumnen som VARCHAR(25). 
- Undvik att använda [NVARCHAR][NVARCHAR] när du bara behöver VARCHAR.
- Använd om möjligt NVARCHAR(4000) eller VARCHAR(8000) i stället för NVARCHAR(MAX) eller VARCHAR(MAX).

Om du använder externa PolyBase-tabeller för att läsa in tabellerna får tabellradens definierade längd inte överstiga 1 MB. När en rad med data med variabel längd överstiger 1 MB kan du läsa in raden med BCP, men inte med PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifiera datatyper som inte stöds
Om du migrerar databasen från en annan SQL-databas kan du stöta på datatyper som inte stöds i SQL Data Warehouse. Använd den här frågan om du vill identifiera datatyper som inte stöds i det befintliga SQL-schemat.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Lösningar för datatyper som inte stöds

Följande lista visar de datatyper som SQL Data Warehouse inte stöder och ger alternativ som du kan använda i stället för datatyperna som inte stöds.

| Datatyp som inte stöds | Lösning |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [Bild](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[Varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [Sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Dela upp kolumnen i flera starkt skrivna kolumner. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Konvertera till temporära tabeller. |
| [Tidsstämpel](/sql/t-sql/data-types/date-and-time-types) |Omarbeta kod för att använda [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) och [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funktion. Endast konstanter stöds som standardvärden, därför kan current_timestamp inte definieras som ett standardvillkor. Om du behöver migrera radversionsvärden från en maskinskriven tidsstämpelkolumn använder du [BINÄRA](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) eller [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) för EJ NULL- eller NULL-radversionsvärden. |
| [Xml](/sql/t-sql/xml/xml-transact-sql) |[Varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [användardefinierad typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Konvertera tillbaka till den inbyggda datatypen när det är möjligt. |
| Standardvärden | Standardvärden stöder endast litteraler och konstanter. |


## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns i [Tabellöversikt](sql-data-warehouse-tables-overview.md).
