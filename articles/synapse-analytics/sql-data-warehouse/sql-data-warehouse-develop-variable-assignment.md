---
title: Tilldela variabler
description: I den här artikeln hittar du viktiga tips för att tilldela T-SQL-variabler i SQL-poolen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0cbadc10fb817c70bd259397c840aae68abc2d54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213337"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Tilldela variabler i Synapse SQL-pool

I den här artikeln hittar du viktiga tips för att tilldela T-SQL-variabler i SQL-poolen.

## <a name="set-variables-with-declare"></a>Ange variabler med deklarera

Variabler i SQL-poolen anges med `DECLARE` instruktionen eller `SET` instruktionen. Att initiera variabler med DECLARE är ett av de mest flexibla sätten att ange ett variabel värde i SQL-poolen.

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

Det går inte att initiera och använda en variabel i samma DECLARE-instruktion. Följande exempel tillåts **inte** eftersom @p1 är både initierad och används i samma declare-instruktion för att illustrera punkten. Därför ger följande exempel ett fel:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Ange värden med SET

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
