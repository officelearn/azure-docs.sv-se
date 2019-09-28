---
title: KVAR i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2e175e1ed62a4afb2a532add161dd2ab63ba9b1c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349771"
---
# <a name="left-azure-cosmos-db"></a>VÄNSTER (Azure Cosmos DB)
 Returnerar den vänstra delen av en sträng med det angivna antalet tecken.  
  
## <a name="syntax"></a>Syntax
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är sträng uttrycket som används för att extrahera tecken från.  
  
*num_expr*  
   Är ett numeriskt uttryck som anger antalet tecken.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras den vänstra delen av ”abc” för värden för olika längd.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
