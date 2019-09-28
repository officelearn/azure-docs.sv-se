---
title: RAND i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function RAND i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4a0672000e630c9e06df84d9c2da5cb8b988c05a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349598"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Returnerar ett slumpmässigt genererat numeriskt värde från [0, 1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Retur typer

  Returnerar ett numeriskt uttryck.

## <a name="remarks"></a>Kommentarer

  `RAND` är en icke-deterministisk funktion. Upprepade anrop av `RAND` returnerar inte samma resultat.

## <a name="examples"></a>Exempel
  
  I följande exempel returneras ett slumpmässigt genererat numeriskt värde.
  
```sql
SELECT RAND() AS rand 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
