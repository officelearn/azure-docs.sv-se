---
title: LOGGA i Azure Cosmos DB-frågespråk
description: Lär dig mer om FUNKTIONEN LOG SQL-system i Azure Cosmos DB för att returnera den naturliga logaritmen för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302516"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 Returnerar den naturliga logaritmen för det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
*base*  
   Valfria numeriska argument som anger basen för logaritmen.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Anmärkningar
  
  Som standard returnerar LOG() den naturliga logaritmen. Du kan ändra basen för logaritmen till ett annat värde med hjälp av den valfria basparametern.  
  
  Den naturliga logaritmen är logaritmen till basen **e**, där **e** är en irrationell konstant ungefär lika med 2,718281828.  
  
  Den naturliga logaritmen för ett tal är själva numret: LOG( EXP( n ) = n. Och exponentialen av den naturliga logaritmen av ett nummer är själva numret: EXP( LOG( n ) ) = n.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel deklareras en variabel och logaritmen för den angivna variabeln (10) returneras.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 I följande exempel `LOG` beräknas för exponenten för ett tal.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
