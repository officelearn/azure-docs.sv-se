---
title: LÄGRE Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen lägre i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3a9c122ef65772458b832d3b1651e7e63e42985e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349702"
---
# <a name="lower-azure-cosmos-db"></a>LÄGRE (Azure Cosmos DB)
 Returnerar ett stränguttryck efter att teckendata med versaler har konverterats till gemener.  
  
## <a name="syntax"></a>Syntax
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `LOWER` i en fråga.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
