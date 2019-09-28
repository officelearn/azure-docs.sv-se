---
title: Logga in Azure Cosmos DB frågespråk
description: Läs om SQL system-funktionen i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 16f2321eb48afacbc9636b5c0588b1ea3a01a284
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349732"
---
# <a name="log-azure-cosmos-db"></a>LOGG (Azure Cosmos DB)
 Returnerar den naturliga logaritmen för det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
*grund*  
   Valfritt numeriska argument som anger basen för logaritmen.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Kommentarer
  
  Som standard returnerar LOG() den naturliga logaritmen. Du kan ändra logaritmens bas till ett annat värde med hjälp av den grundläggande icke obligatoriska parametern.  
  
  Den naturliga logaritmen är logaritmen för talet **e**, där **e** är en onormal konstant ungefär lika 2.718281828.  
  
  Den naturliga logaritmen av exponenten för ett tal är själva siffran: LOG (EXP (n)) = n. Och exponenten för den naturliga logaritmen för ett tal är själva siffran: EXP (LOG (n)) = n.  
  
## <a name="examples"></a>Exempel
  
  Exemplet nedan deklarerar en variabel och Returnerar logaritmen värdet för den angivna variabeln (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 I följande exempel beräknas `LOG` för exponenten för ett tal.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
