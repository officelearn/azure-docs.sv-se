---
title: Tilldela variabler
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
ms.custom: seo-lt-2019
ms.openlocfilehash: 1ae5285a8d1cf6fa391c082d0196b213e6b6a9c5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692758"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Tilldela variabler i Azure SQL Data Warehouse

Tips för att tilldela T-SQL-variabler i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="setting-variables-with-declare"></a>Anger variabler med DECLARE

Variabler i SQL Data Warehouse anges med hjälp av `DECLARE`-instruktionen eller `SET`-instruktionen. Att initiera variabler med deklarera är ett av de mest flexibla sätten att ange ett variabel värde i SQL Data Warehouse.

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

Det går inte att initiera och använda en variabel i samma DECLARE-instruktion. För att illustrera punkten är följande exempel **inte** tillåtet eftersom @p1 både initieras och används i samma declare-instruktion. Följande exempel ger ett fel.

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
