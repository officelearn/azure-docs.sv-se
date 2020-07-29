---
title: ATN2 i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen ATN2 i SQL system i Azure Cosmos DB Returnerar Huvudvärdet för arcus tangens för y/x, uttryckt i radianer
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302669"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Returnerar huvud värde för arcus tangens för y/x, uttryckt i radianer.  
  
## <a name="syntax"></a>Syntax
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel beräknas ATN2 för de angivna x-och y-komponenterna.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
