---
title: REPLIKERA i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen REPLIKERA i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aea29cfff6b3827cfb9169722e48120e3a5a3709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88794322"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKERA (Azure Cosmos DB)
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

  Den maximala längden på resultatet är 10 000 tecken, t. ex. (längd (*str_expr*) *  *num_expr*) <= 10 000. Den här system funktionen kommer inte att använda indexet.

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
