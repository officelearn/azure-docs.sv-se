---
title: Logga in Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen LOG SQL system i Azure Cosmos DB att returnera den naturliga logaritmen för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 74d07076fb322214348d52fe65dbc98bdff3cd44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798269"
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
  
*base*  
   Valfritt numeriskt argument som anger basen för logaritmen.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Kommentarer
  
  Som standard returnerar LOG () den naturliga logaritmen. Du kan ändra logaritmens bas till ett annat värde med hjälp av den valfria bas parametern.  
  
  Den naturliga logaritmen är logaritmen för basen **e**, där **e** är en onormal konstant ungefär lika med 2,718281828.  
  
  Den naturliga logaritmen av exponenten för ett tal är själva siffran: LOG (EXP (n)) = n. Och exponenten för den naturliga logaritmen för ett tal är själva siffran: EXP (LOG (n)) = n.

  Den här system funktionen kommer inte att använda indexet.
  
## <a name="examples"></a>Exempel
  
  Följande exempel deklarerar en variabel och returnerar logaritm svärdet för den angivna variabeln (10).  
  
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
