---
title: Tilldela variabler i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att tilldela T-SQL-variabler i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6c943478f3904aac17a572f012f2b2b69ffa2223
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479549"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Tilldela variabler i Azure SQL Data Warehouse

Tips för att tilldela T-SQL-variabler i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="setting-variables-with-declare"></a>Anger variabler med DECLARE

Variabler i SQL Data Warehouse anges med `DECLARE` instruktionen `SET` eller instruktionen. Att initiera variabler med deklarera är ett av de mest flexibla sätten att ange ett variabel värde i SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Du kan också använda deklarera för att ange mer än en variabel i taget. Du kan inte använda Välj eller uppdatera för att göra följande:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Det går inte att initiera och använda en variabel i samma DECLARE-instruktion. Följande exempel tillåts **inte** eftersom @p1 är både initierad och används i samma declare-instruktion för att illustrera punkten. Följande exempel ger ett fel.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Ange värden med SET

SET är en gemensam metod för att ställa in en enskild variabel.

Följande instruktioner är giltiga sätt att ange en variabel med SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Du kan bara ange en variabel i taget med SET. Sammansatta operatörer är dock tillåtna.

## <a name="limitations"></a>Begränsningar

Du kan inte använda uppdatering för variabel tilldelning.

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).
