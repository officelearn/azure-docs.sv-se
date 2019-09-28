---
title: Under sträng i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function-understräng i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bf14bda9bd1acc62820bf07f83ac074a8d1b691c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349203"
---
# <a name="substring-azure-cosmos-db"></a>Under sträng (Azure Cosmos DB)
 Returnerar en del av ett stränguttryck med början vid den angivna nollbaserade teckenpositionen och fortsätter med den angivna längden eller i slutet av strängen.  
  
## <a name="syntax"></a>Syntax
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.
  
*num_expr1*  
   Är ett numeriskt uttryck som anger start tecken. Värdet 0 är det första bokstaven i *str_expr*.
  
*num_expr2*  
   Är ett numeriskt uttryck som anger det maximala antalet tecken i *str_expr* som ska returneras. Värdet 0 eller mindre resulterar i en tom sträng.

## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel Returnerar delsträngen av ”abc” vid 1 och en längd på 1 tecken.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"substring": "b"}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
