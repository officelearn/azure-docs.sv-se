---
title: ARRAY_CONCAT i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen array concat SQL system i Azure Cosmos DB Returnerar en matris som är resultatet av sammanfogningen av två eller flera mat ris värden
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 10370e16c95f4fc747dd3a66a56794da38562972
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871830"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Returnerar en matris som är resultatet av en sammanfogning av två eller fler matrisvärden.  
  
## <a name="syntax"></a>Syntax
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argument
  
*arr_expr*  
   Är ett mat ris uttryck som sammanfogas till de andra värdena. Funktionen `ARRAY_CONCAT` kräver minst två *arr_expr* argument.  
  
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
