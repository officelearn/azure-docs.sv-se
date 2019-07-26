---
title: Definiera data typer – Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer för att definiera tabell data typer i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 61bb977271186699b0a72389e1538573f978c56b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479369"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tabell data typer i Azure SQL Data Warehouse
Rekommendationer för att definiera tabell data typer i Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Vilka är data typerna?

SQL Data Warehouse stöder de vanligaste data typerna. En lista över data typer som stöds finns i [data typer](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) i instruktionen CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimera rad längd
Genom att minimera storleken på data typerna förkortas rad längden, vilket leder till bättre prestanda för frågor. Använd den minsta data typen som används för dina data. 

- Undvik att definiera tecken kolumner med en stor standard längd. Om det längsta värdet till exempel är 25 tecken definierar du kolumnen som VARCHAR (25). 
- Undvik att använda [NVARCHAR] [NVARCHAR] när du bara behöver VARCHAR.
- När det är möjligt använder du NVARCHAR (4000) eller VARCHAR (8000) i stället för NVARCHAR (MAX) eller VARCHAR (MAX).

Om du använder PolyBase-externa tabeller för att läsa in dina tabeller, kan tabell radens definierade längd inte överstiga 1 MB. När en rad med data för variabel längd överskrider 1 MB kan du läsa in raden med BCP, men inte med PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifiera data typer som inte stöds
Om du migrerar din databas från en annan SQL-databas kan du stöta på data typer som inte stöds i SQL Data Warehouse. Använd den här frågan för att identifiera data typer som inte stöds i det befintliga SQL-schemat.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Lösningar för data typer som inte stöds

I följande lista visas de data typer som SQL Data Warehouse inte stöder och innehåller alternativ som du kan använda i stället för data typerna som inte stöds.

| Datatyp som inte stöds | Lösning: |
| --- | --- |
| [geometri](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [placering](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) (4000) |
| [avbildning](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Dela upp kolumnen i flera kolumner med strikt typ. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Konvertera till temporära tabeller. |
| [tidsstämpel](/sql/t-sql/data-types/date-and-time-types) |Återwork-koden för att använda [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) och funktionen [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) . Endast konstanter stöds som standard, och därför kan CURRENT_TIMESTAMP inte definieras som en standard begränsning. Om du behöver migrera rad versions värden från en kolumn med tidstämpel, använder du [Binary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) eller [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) för värden som inte är null eller null. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [användardefinierad typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Konvertera tillbaka till den interna data typen när det är möjligt. |
| standardvärden | Standardvärden stöder bara litteraler och konstanter. |


## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns i [tabell översikt](sql-data-warehouse-tables-overview.md).
