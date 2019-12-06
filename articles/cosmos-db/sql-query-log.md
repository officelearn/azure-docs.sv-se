---
title: Logga in Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen LOG SQL system i Azure Cosmos DB att returnera den naturliga logaritmen för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ae7812670da836efa326b9224547e4d1b64374c2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873292"
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
  
  Den naturliga logaritmen för e upphöjt till ett tal är antalet själva: LOG (EXP (n)) = n. Och exponentiell för den naturliga logaritmen för ett tal är antalet själva: EXP (loggning (n)) = n.  
  
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
