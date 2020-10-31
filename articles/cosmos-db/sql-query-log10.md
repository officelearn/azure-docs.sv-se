---
title: LOG10 i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL-Funktionen LOG10 i Azure Cosmos DB att returnera 10-logaritmen för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 003479e4b9afaa3895bf18b7d2fc33979197d635
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075304"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar 10-logaritmen för det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expression*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Kommentarer
  
  LOG10-och POWER-funktionerna är inverterade i förhållande till varandra. Till exempel 10 ^ LOG10 (n) = n. Den här system funktionen kommer inte att använda indexet.
  
## <a name="examples"></a>Exempel
  
  Följande exempel deklarerar en variabel och returnerar LOG10-värdet för den angivna variabeln (100).  
  
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
