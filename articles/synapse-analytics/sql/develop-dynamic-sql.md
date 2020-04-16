---
title: Använda dynamisk SQL i Synapse SQL
description: Tips om hur du använder dynamisk SQL i Synapse SQL.
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
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429608"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dynamisk SQL i Synapse SQL
I den här artikeln hittar du tips om hur du använder dynamisk SQL och utvecklar lösningar med Synapse SQL.

## <a name="dynamic-sql-example"></a>Dynamiskt SQL-exempel

När du utvecklar programkod kan du behöva använda dynamisk SQL för att leverera flexibla, generiska och modulära lösningar.

> [!NOTE]
> SQL-poolen stöder inte blob-datatyper just nu. Att inte stödja blob-datatyper kan begränsa storleken på dina strängar eftersom blob-datatyper innehåller både varchar(max) och nvarchar(max) typer. Om du har använt dessa typer i programkoden för att skapa stora strängar måste du dela upp koden i segment och använda EXEC-satsen i stället.

Ett enkelt exempel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Om strängen är kort kan du använda [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) som vanligt.

> [!NOTE]
> Satser som körs som dynamisk SQL kommer fortfarande att omfattas av alla T-SQL-valideringsregler.

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [utvecklingsöversikt](develop-overview.md).
