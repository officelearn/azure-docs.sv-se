---
title: ASIN i Azure Cosmos DB-frågespråk
description: Lär dig mer om hur ARCSine (ASIN) SQL-systemfunktionen i Azure Cosmos DB returnerar vinkeln, i radianer, vars sinus är det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302703"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Returnerar vinkeln i radianer vars sinus är det angivna numeriska uttrycket. Detta kallas också arcsin.  
  
## <a name="syntax"></a>Syntax
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel `ASIN` returneras av -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
