---
title: SUBSTRING i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktion SUBSTRING i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303706"
---
# <a name="substring-azure-cosmos-db"></a>DELSTRÄNG (Azure Cosmos DB)
 Returnerar en del av ett stränguttryck som börjar vid den angivna teckenfria positionen och fortsätter till den angivna längden eller till strängens.  
  
## <a name="syntax"></a>Syntax
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck.
  
*num_expr1*  
   Är ett numeriskt uttryck som anger starttecknet. Värdet 0 är det första tecknet *i str_expr*.
  
*num_expr2*  
   Är ett numeriskt uttryck som anger det maximala antalet tecken *str_expr* som ska returneras. Värdet 0 eller mindre resulterar i tom sträng.

## <a name="return-types"></a>Returtyper
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras delsträngen för "abc" från 1 och för en längd på 1 tecken.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer att dra nytta `0`av ett [intervallindex](index-policy.md#includeexclude-strategy) om utgångsläget är .

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
