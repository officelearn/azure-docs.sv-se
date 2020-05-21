---
title: ENDSWITH i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen ENDSWITH SQL system i Azure Cosmos DB att returnera ett booleskt värde som anger om det första sträng uttrycket slutar med det andra
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d7e7f3e33389d4a201ec3281829cb9f0415978e6
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713564"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

 Returnerar ett booleskt värde som anger om det första sträng uttrycket slutar med det andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är ett sträng uttryck.  
  
*str_expr2*  
   Är ett sträng uttryck som ska jämföras med slutet av *str_expr1*.

*bool_expr* Valfritt värde för att ignorera Skift läge. När värdet är true kommer ENDSWITH att göra en Skift läges känslig sökning. Värdet är false när det har angetts.
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras "ABC"-slut för "b" och "BC".  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
