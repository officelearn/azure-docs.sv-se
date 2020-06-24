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
ms.openlocfilehash: 91e251c8274ad85fcb0b3a65a3bdbcc660ceba00
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101018"
---
# <a name="assigning-variables-with-synapse-sql"></a>Tilldela variabler med Synapse SQL

I den här artikeln hittar du tips om hur du tilldelar T-SQL-variabler med Synapse SQL.

## <a name="setting-variables-with-declare"></a>Anger variabler med DECLARE

Variabler i Synapse SQL anges med `DECLARE` instruktionen eller `SET` instruktionen. Att initiera variabler med DECLARE är ett av de mest flexibla sätten att ange ett variabel värde i Synapse SQL.

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

Det går inte att initiera och använda en variabel i samma DECLARE-instruktion. Följande exempel är inte tillåtet eftersom * \@ P1* både initieras och används i samma declare-instruktion. Följande exempel ger ett fel.

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

Mer utvecklings tips finns i artikeln [SYNAPSE SQL Development Overview](develop-overview.md) .
