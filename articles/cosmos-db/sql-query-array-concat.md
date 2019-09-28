---
title: ARRAY_CONCAT i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ARRAY_CONCAT i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ad973650ac205313f9045c170f99e15e385a82d1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348727"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Returnerar en matris som är resultatet av en sammanfogning av två eller fler matrisvärden.  
  
## <a name="syntax"></a>Syntax
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argument
  
*arr_expr*  
   Är ett mat ris uttryck som sammanfogas till de andra värdena. Funktionen `ARRAY_CONCAT` kräver minst två *arr_expr* -argument.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett matrisuttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel hur du sammanfoga två matriser.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  

## <a name="next-steps"></a>Nästa steg

- [Array Functions Azure Cosmos DB](sql-query-array-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
