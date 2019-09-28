---
title: LOG10 i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function LOG10 i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 56f1e96e7d4ee1b5f38ee7392665e17819ae554b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349730"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Returnerar 10-logaritmen av uttryck.  
  
## <a name="syntax"></a>Syntax
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*Numeric_Expression*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Kommentarer
  
  Funktionerna LOG10 och POWER är omvänt relaterade till varandra. Till exempel 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Exempel
  
  Exemplet nedan deklarerar en variabel och returnerar LOG10 värdet för den angivna variabeln (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
