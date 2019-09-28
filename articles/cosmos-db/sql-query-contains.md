---
title: INNEHÅLLER i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5d5018d0173c316a0a31bd2f70e15e5ff972d153
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351090"
---
# <a name="contains-azure-cosmos-db"></a>INNEHÅLLER (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger huruvida det första stränguttrycket innehåller det andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är sträng uttrycket som ska genomsökas.  
  
*str_expr2*  
   Är sträng uttrycket som ska hittas.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrol leras om "ABC" innehåller "AB" och om "ABC" innehåller "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
