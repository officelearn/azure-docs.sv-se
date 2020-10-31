---
title: REPLIKERA i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen REPLIKERA i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8b1fc1cb28b62a388df53238df58420bd3317a8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082869"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKERA (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Upprepar ett strängvärde ett angivet antal gånger.
  
## <a name="syntax"></a>Syntax
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.
  
*num_expr*  
   Är ett numeriskt uttryck. Om *num_expr* är negativt eller ej ändligt är resultatet odefinierat.
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett sträng uttryck.
  
## <a name="remarks"></a>Kommentarer

  Den maximala längden på resultatet är 10 000 tecken, t. ex. (längd ( *str_expr* ) *  *num_expr* ) <= 10 000. Den här system funktionen kommer inte att använda indexet.

## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `REPLICATE` i en fråga.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Här är resultatuppsättningen.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
