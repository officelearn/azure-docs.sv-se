---
title: REPLIKERA i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen REPLIKERA i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302193"
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
  
  Returnerar ett stränguttryck.
  
## <a name="remarks"></a>Anmärkningar
  Den maximala längden på resultatet är 10 000 tecken, t. ex. (längd (*str_expr*) * *num_expr*) < = 10 000.

## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `REPLICATE` i en fråga.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Här är resultatuppsättningen.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Anmärkningar

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
