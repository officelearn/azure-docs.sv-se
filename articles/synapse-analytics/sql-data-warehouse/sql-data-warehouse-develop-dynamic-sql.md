---
title: Använda dynamisk SQL
description: Tips om hur du använder dynamisk SQL i Azure SQL Data Warehouse för att utveckla lösningar.
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
ms.openlocfilehash: a44bec72029a50c2ef348bcdda497803e35f586d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350561"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamisk SQL i SQL Data Warehouse
Tips om hur du använder dynamisk SQL i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="dynamic-sql-example"></a>Dynamiskt SQL-exempel

När du utvecklar programkod för SQL Data Warehouse kan du behöva använda dynamisk sql för att leverera flexibla, generiska och modulära lösningar. SQL Data Warehouse stöder inte blob-datatyper just nu. Att inte stödja blob-datatyper kan begränsa storleken på dina strängar eftersom blob-datatyper innehåller både varchar(max) och nvarchar(max) typer. Om du har använt dessa typer i programkoden för att skapa stora strängar måste du dela upp koden i segment och använda EXEC-satsen i stället.

Ett enkelt exempel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Om strängen är kort kan du använda [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) som vanligt.

> [!NOTE]
> Satser som körs som dynamisk SQL kommer fortfarande att omfattas av alla TSQL-valideringsregler.
> 
> 

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

