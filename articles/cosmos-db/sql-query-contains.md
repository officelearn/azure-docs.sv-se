---
title: INNEHÅLLER I Azure Cosmos DB-frågespråk
description: Lär dig mer om hur funktionen CONTAINS SQL-system i Azure Cosmos DB returnerar en boolesk som anger om det första stränguttrycket innehåller det andra
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302601"
---
# <a name="contains-azure-cosmos-db"></a>INNEHÅLLER (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger huruvida det första stränguttrycket innehåller det andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är stränguttrycket som ska sökas igenom.  
  
*str_expr2*  
   Är stränguttrycket att hitta.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrolleras om "abc" innehåller "ab" och om "abc" innehåller "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
