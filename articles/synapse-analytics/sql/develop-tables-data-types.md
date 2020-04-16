---
title: Definiera datatyper
description: Rekommendationer för att definiera tabelldatatyper i Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 312c58a7df09ebe3e0bdf0749f902199723a331b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429010"
---
# <a name="table-data-types-in-synapse-sql"></a>Tabelldatatyper i Synapse SQL

Rekommendationer för att definiera tabelldatatyper i Synapse SQL. 

## <a name="what-are-the-data-types"></a>Vilka är datatyperna?

Synapse SQL stöder de vanligaste datatyperna. En lista över datatyper som stöds finns i [datatyper](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) i CREATE TABLE-satsen. 

## <a name="minimize-row-length"></a>Minimera radlängd

Om du minimerar storleken på datatyper förkortas radlängden, vilket leder till bättre frågeprestanda. Använd den minsta datatyp som fungerar för dina data.

- Undvik att definiera teckenkolumner med en stor standardlängd. Om till exempel det längsta värdet är 25 tecken definierar du kolumnen som VARCHAR(25).
- Undvik att använda [NVARCHAR][NVARCHAR] när du bara behöver VARCHAR.
- Använd om möjligt NVARCHAR(4000) eller VARCHAR(8000) i stället för NVARCHAR(MAX) eller VARCHAR(MAX).

> [!NOTE]
> Om du använder externa PolyBase-tabeller för att läsa in SQL-biljardtabellerna får tabellradens definierade längd inte överstiga 1 MB. När en rad med data med variabel längd överstiger 1 MB kan du läsa in raden med BCP, men inte med PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifiera datatyper som inte stöds

Om du migrerar databasen från en annan SQL-databas kan du stöta på datatyper som inte stöds i Synapse SQL. Använd den här frågan om du vill identifiera datatyper som inte stöds i det befintliga SQL-schemat.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Lösningar för datatyper som inte stöds

Följande lista visar de datatyper som Synapse SQL inte stöder och ger alternativ som du kan använda i stället för datatyperna som inte stöds.

| Datatyp som inte stöds | Lösning |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [Bild](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[Varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Dela upp kolumnen i flera starkt skrivna kolumner. |
| [table](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Om du använder SQL-poolen kan du konvertera till temporära tabeller. Om du använder SQL (förhandsversion) kan du överväga att lagra data till lagring med [CETAS](../sql/develop-tables-cetas.md). |
| [Tidsstämpel](/sql/t-sql/data-types/date-and-time-types) |Omarbeta kod för att använda [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funktion. Endast konstanter stöds som standardvärden, därför kan current_timestamp inte definieras som ett standardvillkor. Om du behöver migrera radversionsvärden från en maskinskriven tidsstämpelkolumn använder du [BINÄRA](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) eller [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) för EJ NULL- eller NULL-radversionsvärden. |
| [Xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[Varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [användardefinierad typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Konvertera tillbaka till den inbyggda datatypen när det är möjligt. |
| Standardvärden | Standardvärden stöder endast litteraler och konstanter. |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i [Tabellöversikt](develop-overview.md).
