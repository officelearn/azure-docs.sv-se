---
title: ENDSWITH i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen ENDSWITH SQL system i Azure Cosmos DB att returnera ett booleskt värde som anger om det första sträng uttrycket slutar med det andra
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b3e692877faab8a8d507a44068d4cdfdc73a916
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873360"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger om först stränguttryck slutar med andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är ett sträng uttryck.  
  
*str_expr2*  
   Är ett sträng uttryck som ska jämföras med slutet av *str_expr1*.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras ”abc” slutar med ”b” och ”bc”.  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
