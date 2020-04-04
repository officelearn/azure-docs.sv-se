---
title: Definiera datatyper
description: Rekommendationer för att definiera tabelldatatyper i Synapse SQL-pool.
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
ms.openlocfilehash: 5b3d24232c26bb4d483d360eb593bc361190ccfe
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631244"
---
# <a name="table-data-types-in-synapse-sql-pool"></a>Tabelldatatyper i Synapse SQL-pool
I den här artikeln finns rekommendationer för att definiera tabelldatatyper i SQL-poolen. 

## <a name="supported-data-types"></a>Datatyper som stöds

SQL-poolen stöder de vanligaste datatyperna. En lista över datatyper som stöds finns i [datatyper](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) i CREATE TABLE-satsen. 

## <a name="minimize-row-length"></a>Minimera radlängd
Om du minimerar storleken på datatyper förkortas radlängden, vilket leder till bättre frågeprestanda. Använd den minsta datatyp som fungerar för dina data. 

- Undvik att definiera teckenkolumner med en stor standardlängd. Om till exempel det längsta värdet är 25 tecken definierar du kolumnen som VARCHAR(25). 
- Undvik att använda [NVARCHAR](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) när du bara behöver VARCHAR.
- Använd om möjligt NVARCHAR(4000) eller VARCHAR(8000) i stället för NVARCHAR(MAX) eller VARCHAR(MAX).

Om du använder externa PolyBase-tabeller för att läsa in tabellerna får tabellradens definierade längd inte överstiga 1 MB. När en rad med data med variabel längd överstiger 1 MB kan du läsa in raden med BCP, men inte med PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifiera datatyper som inte stöds
Om du migrerar databasen från en annan SQL-databas kan du hitta datatyper som inte stöds i SQL-poolen. Använd följande fråga för att identifiera datatyper som inte stöds i det befintliga SQL-schemat:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Lösningar för datatyper som inte stöds

Följande lista visar de datatyper som SQL-poolen inte stöder och ger användbara alternativ för datatyper som inte stöds.

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
| [Tidsstämpel](/sql/t-sql/data-types/date-and-time-types) |Omarbeta kod för att använda [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) och [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funktion. Endast konstanter stöds som standardvärden, så current_timestamp kan inte definieras som ett standardvillkor. Om du behöver migrera radversionsvärden från en maskinskriven tidsstämpelkolumn använder du [BINÄR (8)](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)eller [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) för EJ NULL- eller NULL-radversionsvärden. |
| [Xml](/sql/t-sql/xml/xml-transact-sql) |[Varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [användardefinierad typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Konvertera tillbaka till den inbyggda datatypen när det är möjligt. |
| Standardvärden | Standardvärden stöder endast litteraler och konstanter. |


## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i [Tabellöversikt](sql-data-warehouse-tables-overview.md).
