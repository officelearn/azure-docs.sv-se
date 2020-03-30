---
title: GRADER i Azure Cosmos DB-frågespråk
description: Lär dig mer om FUNKTIONEN DEGREES SQL-system i Azure Cosmos DB för att returnera motsvarande vinkel i grader för en vinkel som anges i radianer
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299478"
---
# <a name="degrees-azure-cosmos-db"></a>GRADER (Azure Cosmos DB)
 Returnerar motsvarande vinkel i grader för en vinkel som anges i radianer.  
  
## <a name="syntax"></a>Syntax
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras antalet grader i en vinkel på PI/2-radianer.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
