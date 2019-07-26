---
title: Använda dynamisk SQL i Azure SQL Data Warehouse | Microsoft Docs
description: Tips om hur du använder dynamisk SQL i Azure SQL Data Warehouse för att utveckla lösningar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4454b1d44d0be61dca8571e86c73e09a9527d1eb
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479665"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamisk SQL i SQL Data Warehouse
Tips om hur du använder dynamisk SQL i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="dynamic-sql-example"></a>Dynamiskt SQL-exempel

När du utvecklar program kod för SQL Data Warehouse kan du behöva använda dynamisk SQL för att leverera flexibla, generiska och modulära lösningar. SQL Data Warehouse har inte stöd för BLOB-datatyper just nu. Om du inte stöder BLOB-datatyper kan du begränsa storleken på dina strängar eftersom BLOB-datatyper inkluderar båda typerna varchar (max) och nvarchar (max). Om du har använt dessa typer i program koden för att bygga stora strängar måste du dela upp koden i segment och använda EXEC-instruktionen i stället.

Ett enkelt exempel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Om strängen är kort kan du använda [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) som normalt.

> [!NOTE]
> Instruktioner som körs i dynamisk SQL gäller fortfarande alla TSQL verifierings regler.
> 
> 

## <a name="next-steps"></a>Nästa steg
Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).

