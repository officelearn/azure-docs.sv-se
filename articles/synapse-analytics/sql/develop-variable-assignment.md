---
title: Tilldela variabler med Synapse SQL
description: I den här artikeln hittar du tips om hur du tilldelar T-SQL-variabler med Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428672"
---
# <a name="assigning-variables-with-synapse-sql"></a>Tilldela variabler med Synapse SQL

I den här artikeln hittar du tips om hur du tilldelar T-SQL-variabler med Synapse SQL.

## <a name="setting-variables-with-declare"></a>Ange variabler med DEKLARERA

Variabler i Synapse SQL `DECLARE` anges med `SET` hjälp av uttrycket eller uttrycket. Att initiera variabler med DECLARE är ett av de mest flexibla sätten att ange ett variabelvärde i Synapse SQL.

```sql
DECLARE @v  int = 0
;
```

Du kan också använda DECLARE för att ange mer än en variabel i taget. Du kan inte använda SELECT eller UPDATE för att göra följande:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Du kan inte initiera och använda en variabel i samma DECLARE-sats. För att illustrera är följande exempel *@p1* inte tillåtet eftersom både initieras och används i samma DECLARE-sats. Följande exempel ger ett fel.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Ange värden med SET

SET är en vanlig metod för att ange en enda variabel.

Följande satser är alla giltiga sätt att ange en variabel med SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Du kan bara ställa in en variabel i taget med SET. Sammansatta operatörer är dock tillåtna.

## <a name="limitations"></a>Begränsningar

Du kan inte använda UPDATE för variabel tilldelning.

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [översiktsartikeln för synaps-SQL-utveckling.](develop-overview.md)