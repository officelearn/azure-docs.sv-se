---
title: TAN i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function-TAN i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70636dbc00307ced8ad0cf84f41fe025c759997e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349155"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Returnerar tangens för den angivna vinkeln i radianer i det angivna uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel beräknar tangens för PI () / 2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
