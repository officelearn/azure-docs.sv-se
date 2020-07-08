---
title: ARRAY_CONCAT i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen array concat SQL system i Azure Cosmos DB Returnerar en matris som är resultatet av sammanfogningen av två eller flera mat ris värden
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
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
   Är ett mat ris uttryck som sammanfogas till de andra värdena. `ARRAY_CONCAT`Funktionen kräver minst två *arr_expr* argument.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett mat ris uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kan du sammanfoga två matriser.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Array Functions Azure Cosmos DB](sql-query-array-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
