---
title: REPLIKERA i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionsdrappering i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
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
   Är ett stränguttryck.
  
*num_expr*  
   Är ett numeriskt uttryck. Om *num_expr* är negativt eller icke-begränsat är resultatet odefinierat.
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett stränguttryck.
  
## <a name="remarks"></a>Anmärkningar
  Den maximala längden på resultatet är 10 000 tecken, dvs <*num_expr**str_expr.*

## <a name="examples"></a>Exempel
  
  I följande exempel visas `REPLICATE` hur du använder i en fråga.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Här är resultatuppsättningen.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
