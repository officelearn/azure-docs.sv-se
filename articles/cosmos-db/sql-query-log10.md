---
title: LOG10 i Azure Cosmos DB-frågespråk
description: Lär dig mer om funktionen LOG10 SQL-system i Azure Cosmos DB för att returnera logaritmen för bas-10 för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302499"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Returnerar logaritmen bas-10 för det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expression*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Anmärkningar
  
  Funktionerna LOG10 och POWER är omvänt relaterade till varandra. Till exempel 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel deklareras en variabel och LOG10-värdet för den angivna variabeln (100) returneras.  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
