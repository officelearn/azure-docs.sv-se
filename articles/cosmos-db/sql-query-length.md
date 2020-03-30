---
title: LÄNGD i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen LENGTH i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303723"
---
# <a name="length-azure-cosmos-db"></a>LÄNGD (Azure Cosmos DB)
 Returnerar antalet tecken i det angivna stränguttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är stränguttrycket som ska utvärderas.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras längden på en sträng.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
