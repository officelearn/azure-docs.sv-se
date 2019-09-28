---
title: ÖVRE i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen i övre Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b0f025948803a23c5b3c8bb6415c0e111b946b2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349035"
---
# <a name="upper-azure-cosmos-db"></a>ÖVRE (Azure Cosmos DB)
 Returnerar ett stränguttryck efter att teckendata med gemener har konverterats till versaler.  
  
## <a name="syntax"></a>Syntax
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `UPPER` i en fråga  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
