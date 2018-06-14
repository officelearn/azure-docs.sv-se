---
title: Med dynamisk SQL i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att använda dynamisk SQL i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 604074e0a645918f7033360b79a1b7cad050c9e4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523174"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamisk SQL i SQL Data Warehouse
Tips för att använda dynamisk SQL i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="dynamic-sql-example"></a>Dynamisk SQL-exempel

När du utvecklar programkod för SQL Data Warehouse, kan du behöva använda dynamisk sql ger flexibla, allmän och modulär lösningar. SQL Data Warehouse stöder inte blob-datatyper just nu. Stöder inte blob-datatyper kan begränsa storleken på din strängar eftersom blob-datatyper är både varchar(max) och nvarchar(max) typer. Om du har använt dessa typer i din programkod för att skapa stora strängar, måste du bryta koden i segment och i stället använda EXEC-instruktion.

Ett enkelt exempel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Om strängen är kort, kan du använda [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) som vanligt.

> [!NOTE]
> Instruktioner som körs som dynamiska SQL fortfarande omfattas av alla TSQL valideringsregler.
> 
> 

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

