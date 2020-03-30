---
title: ARRAY_CONCAT i Azure Cosmos DB-frågespråk
description: Lär dig mer om hur matrisen Concat SQL-systemfunktionen i Azure Cosmos DB returnerar en matris som är resultatet av att sammanfoga två eller flera matrisvärden
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295886"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Returnerar en matris som är resultatet av en sammanfogning av två eller fler matrisvärden.  
  
## <a name="syntax"></a>Syntax
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argument
  
*arr_expr*  
   Är ett arrayuttryck att sammanfoga till de andra värdena. Funktionen `ARRAY_CONCAT` kräver minst två *arr_expr* argument.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett arrayuttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kan du sammanfoga två matriser.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matrisfunktioner Azure Cosmos DB](sql-query-array-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
