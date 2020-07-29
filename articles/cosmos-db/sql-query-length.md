---
title: LÄNGD i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen längd i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303723"
---
# <a name="length-azure-cosmos-db"></a>LÄNGD (Azure Cosmos DB)
 Returnerar antalet tecken i det angivna sträng uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är det sträng uttryck som ska utvärderas.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras längden för en sträng.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
