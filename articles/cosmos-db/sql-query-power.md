---
title: POWER i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen POWER i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349630"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
 Returnerar värdet för det angivna uttrycket till den angivna effekten.  
  
## <a name="syntax"></a>Syntax
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr1*  
   Är ett numeriskt uttryck.  
  
*numeric_expr2*  
   Är den makt som att höja *numeric_expr1*.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  Följande exempel visar att höja ett tal till kraften av 3 (kuben av antalet).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
