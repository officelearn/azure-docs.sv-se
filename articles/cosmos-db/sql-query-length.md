---
title: LÄNGD i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen längd i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349750"
---
# <a name="length-azure-cosmos-db"></a>LÄNGD (Azure Cosmos DB)
 Returnerar antalet tecken i angivet stränguttryck.  
  
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
  
  I följande exempel returnerar längden på en sträng.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"len": 3}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
