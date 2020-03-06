---
title: HÖGER i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL-systemfunktionen direkt i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302159"
---
# <a name="right-azure-cosmos-db"></a>HÖGER (Azure Cosmos DB)
 Returnerar den högra delen av en sträng med det angivna antalet tecken.  
  
## <a name="syntax"></a>Syntax
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är sträng uttrycket som används för att extrahera tecken från.  
  
*num_expr*  
   Är ett numeriskt uttryck som anger antalet tecken.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras den högra delen av ”abc” för värden för olika längd.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
