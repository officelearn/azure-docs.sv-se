---
title: Tilldela variabler i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att tilldela T-SQL-variabler i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 09b0ee336ce00eb20ea501cd97833dfdd6540b30
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598752"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Tilldela variabler i Azure SQL Data Warehouse
Tips för att tilldela T-SQL-variabler i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="setting-variables-with-declare"></a>Ange variabler med DECLARE
Variabler i SQL Data Warehouse anges med hjälp av den `DECLARE` instruktionen eller `SET` instruktionen. Initiera variabler med DECLARE är en av de mest flexibla sätten att ange ett värde för variabeln i SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Du kan också använda DECLARE för att ange fler än en variabel i taget. Du kan inte använda SELECT- eller UPDATE för att göra följande:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Du kan inte initieras och använda en variabel i samma DECLARE-sats. För att illustrera punkten i följande exempel är **inte** tillåts eftersom @p1 är både initierats och användas i samma DECLARE-sats. I följande exempel får ett fel.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Ange värden med
Är en vanlig metod för att ställa in en enskild variabel.

Följande instruktioner är giltiga sätt att ange en variabel med:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Du kan bara ange en variabel i taget med. Dock är sammansatt operatorer tillåtna.

## <a name="limitations"></a>Begränsningar
Du kan inte använda SELECT- eller UPDATE för variabeltilldelning.

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

