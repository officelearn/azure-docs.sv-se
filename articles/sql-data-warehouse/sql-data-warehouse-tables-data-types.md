---
title: 'Definiera datatyper: Azure SQL Data Warehouse | Microsoft Docs'
description: Rekommendationer för att definiera tabellen datatyper i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4d8a222a6d4cfa4138fe833fb4e9cc895dbc5f65
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523514"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tabell datatyper i Azure SQL Data Warehouse
Rekommendationer för att definiera tabellen datatyper i Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Vilka är datatyperna?

SQL Data Warehouse stöder vanligaste de datatyper. En lista över datatyperna som stöds, se [datatyper](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) i CREATE TABLE-instruktion. 

## <a name="minimize-row-length"></a>Minimera radlängden
Minimera storlek på datatyper för förkortar radlängden, vilket resulterar i bättre prestanda. Använd den minsta datatypen som fungerar för dina data. 

- Undvik att definiera teckenkolumner med en stor standardlängden. Till exempel om det längsta värdet är 25 tecken, definiera kolumnen som VARCHAR(25). 
- Undvik att använda [NVARCHAR] [NVARCHAR] när du behöver bara VARCHAR.
- När det är möjligt använda NVARCHAR(4000) eller VARCHAR(8000) i stället för NVARCHAR(MAX) eller VARCHAR(MAX).

Om du använder PolyBase externa tabeller för att läsa in tabellerna får inte tabellraden definierade längd överstiga 1 MB. När en rad med variabel längd överskrider 1 MB, kan du läsa in raden med BCP, men inte med PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifiera datatyper
Om du migrerar databasen från en annan SQL-databas, kan det uppstå datatyper som inte stöds i SQL Data Warehouse. Använd den här frågan för att identifiera datatyper i din befintliga SQL-schemat.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Lösningar för datatyper

I följande lista visas datatyperna som SQL Data Warehouse stöder inte och ger alternativ som du kan använda i stället för typerna av data som inte stöds.

| Datatyp | Lösning |
| --- | --- |
| [geometri](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geografisk plats](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [Bild](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Dela upp kolumn i flera strikt typkontroll kolumner. |
| [Tabell](/sql/t-sql/data-types/table-transact-sql) |Konvertera till temporära tabeller. |
| [tidsstämpel](/sql/t-sql/data-types/date-and-time-types) |Omarbeta kod för att använda [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) och [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funktion. Endast konstanter stöds som standard, därför current_timestamp kan inte definieras som en default-begränsning. Om du behöver migrera version radvärden från en tidsstämpelkolumn skrivna använder [binära](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) eller [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) rad Versionsvärden för inte är NULL eller NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [användardefinierad typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Konvertera tillbaka till den ursprungliga datatypen när det är möjligt. |
| standardvärden | Standardvärden stöder literaler och endast konstanter. |


## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar tabeller finns [tabell översikt](sql-data-warehouse-tables-overview.md).
