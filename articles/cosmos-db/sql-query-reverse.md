---
title: OMVÄND i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen SQL system reversed i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8237918232bd8ba8edb2b8f71440ffd73a913334
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349556"
---
# <a name="reverse-azure-cosmos-db"></a>OMVÄND (Azure Cosmos DB)
 Returnerar den omvända ordningen av ett strängvärde.  
  
## <a name="syntax"></a>Syntax
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `REVERSE` i en fråga.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
