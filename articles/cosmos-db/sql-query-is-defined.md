---
title: IS_DEFINED i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function IS_DEFINED i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c3193262a41b3c6772d4fe29c78a132bc51bbd8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349882"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger huruvida egenskapen har tilldelats ett värde.  
  
## <a name="syntax"></a>Syntax
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*uttrycks*  
   Är ett valfritt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel söker efter förekomsten av en egenskap i det angivna JSON-dokumentet. Först returnerar true eftersom ”a” finns, men andra returnerar FALSKT eftersom ”b” saknas.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Typ kontroll funktioner Azure Cosmos DB](sql-query-type-checking-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
