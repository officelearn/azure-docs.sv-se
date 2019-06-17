---
title: Med hjälp av dynamisk SQL i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att använda dynamisk SQL i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 567637cab1c983992b08f65352ab9a92bd448c5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861811"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamisk SQL i SQL Data Warehouse
Tips för att använda dynamisk SQL i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="dynamic-sql-example"></a>Dynamisk SQL-exempel

När du utvecklar programkod för SQL Data Warehouse, kan du behöva använda dynamisk sql för att leverera flexibla, allmän och modulära lösningar. SQL Data Warehouse stöder inte blob datatyper just nu. Inte har stöd för blob-datatyper kan begränsa storleken på din strängar eftersom blob-datatyper innehåller både varchar(max) och nvarchar(max) typer. Om du har använt typerna i din programkod för att skapa stora strängar kan behöva du dela koden i segment och i stället använda EXEC-instruktion.

Ett enkelt exempel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Om strängen är kort, kan du använda [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) som vanligt.

> [!NOTE]
> Instruktioner som körs som dynamisk SQL kommer fortfarande att omfattas av alla TSQL-valideringsregler.
> 
> 

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

