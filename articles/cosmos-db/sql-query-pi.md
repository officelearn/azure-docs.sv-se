---
title: PI i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function PI i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349649"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Returnerar konstantvärdet för PI.  
  
## <a name="syntax"></a>Syntax
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras värdet för `PI`.  
  
```sql
SELECT PI() AS pi 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
