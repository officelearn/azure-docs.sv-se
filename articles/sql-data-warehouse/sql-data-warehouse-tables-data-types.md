---
title: "Datatyperna vägledning - Azure SQL Data Warehouse | Microsoft Docs"
description: "Rekommendationer för att definiera datatyper som är kompatibla med SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 06/02/2017
ms.author: shigu;barbkess
ms.openlocfilehash: 5c24c71af16bd9851d9caf15fecfa4bb76f5f77e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="guidance-for-defining-data-types-for-tables-in-sql-data-warehouse"></a>Riktlinjer för att definiera datatyper för tabeller i SQL Data Warehouse
Använd de här rekommendationerna för att definiera tabellen datatyper som är kompatibla med SQL Data Warehouse. Förutom kompatibilitet förbättrar minimera storlek på datatyper för prestanda för frågor.

SQL Data Warehouse stöder vanligaste de datatyper. En lista över datatyperna som stöds, se [datatyper](/sql/docs/t-sql/statements/create-table-azure-sql-data-warehouse.md#datatypes) i CREATE TABLE-instruktion. 


## <a name="minimize-row-length"></a>Minimera radlängden
Minimera storlek på datatyper för förkortar radlängden, vilket resulterar i bättre prestanda. Använd den minsta datatypen som fungerar för dina data. 

- Undvik att definiera teckenkolumner med en stor standardlängden. Till exempel om det längsta värdet är 25 tecken, definiera kolumnen som VARCHAR(25). 
- Undvik att använda [NVARCHAR] [ NVARCHAR] när du behöver bara VARCHAR.
- När det är möjligt använda NVARCHAR(4000) eller VARCHAR(8000) i stället för NVARCHAR(MAX) eller VARCHAR(MAX).

Om du använder Polybase för att läsa in tabellerna får inte tabellraden definierade längd överstiga 1 MB. När en rad med variabel längd överskrider 1 MB, kan du läsa in raden med BCP, men inte med PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifiera datatyper
Om du migrerar databasen från en annan SQL-databas, kan det uppstå datatyper som inte stöds i SQL Data Warehouse. Använd den här frågan för att identifiera datatyper i din befintliga SQL-schemat.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Använda lösningar för datatyper

I följande lista visas datatyperna som SQL Data Warehouse stöder inte och ger alternativ som du kan använda i stället för typerna av data som inte stöds.

| Datatyp | Lösning |
| --- | --- |
| [geometri][geometry] |[varbinary][varbinary] |
| [geografisk plats][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [bild][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |Dela upp kolumn i flera strikt typkontroll kolumner. |
| [tabell][table] |Konvertera till temporära tabeller. |
| [tidsstämpel][timestamp] |Omarbeta kod för att använda [datetime2] [ datetime2] och `CURRENT_TIMESTAMP` funktion.  Endast konstanter stöds som standard, därför current_timestamp kan inte definieras som en default-begränsning. Om du behöver migrera version radvärden från en tidsstämpelkolumn skrivna använder [binära][BINARY](8) eller [VARBINARY][BINARY](8) för NOT NULL eller NULL version radvärden. |
| [XML][xml] |[varchar][varchar] |
| [användardefinierad typ][user defined types] |Konvertera tillbaka till den ursprungliga datatypen när det är möjligt. |
| standardvärden | Standardvärden stöder literaler och endast konstanter.  Icke-deterministiska uttryck eller funktioner, t.ex `GETDATE()` eller `CURRENT_TIMESTAMP`, stöds inte. |


## <a name="next-steps"></a>Nästa steg
Du kan läsa mer här:

- [Metodtips för SQL Data Warehouse][SQL Data Warehouse Best Practices]
- [Översikt över tabell][Overview]
- [Distribuera en tabell][Distribute]
- [Indexering av en tabell][Index]
- [Partitionering en tabell][Partition]
- [Underhåll av tabellstatistik][Statistics]
- [Temporära tabeller][Temporary]

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx
