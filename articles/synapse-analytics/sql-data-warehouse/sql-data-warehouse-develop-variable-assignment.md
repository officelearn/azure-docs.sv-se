---
title: Tilldela variabler
description: I den här artikeln hittar du viktiga tips för att tilldela T-SQL-variabler i SQL-poolen.
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
ms.openlocfilehash: 2dcf706ea59657abc2718a69e59191604dc2849d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633409"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Tilldela variabler i Synapse SQL-pool

I den här artikeln hittar du viktiga tips för att tilldela T-SQL-variabler i SQL-poolen.

## <a name="set-variables-with-declare"></a>Ange variabler med DEKLARERA

Variabler i SQL-poolen `DECLARE` anges med `SET` hjälp av uttrycket eller uttrycket. Att initiera variabler med DECLARE är ett av de mest flexibla sätten att ange ett variabelt värde i SQL-poolen.

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

Du kan inte initiera och använda en variabel i samma DECLARE-sats. För att illustrera punkten är **not** följande @p1 exempel inte tillåtet eftersom både initieras och används i samma DECLARE-sats. I följande exempel visas ett fel i följande exempel:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Ange värden med SET

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

Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).
