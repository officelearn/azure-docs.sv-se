---
title: RAND i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen RAND i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302227"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Returnerar ett slumpmässigt genererat numeriskt värde från [0,1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Returtyper

  Returnerar ett numeriskt uttryck.

## <a name="remarks"></a>Anmärkningar

  `RAND`är en icke-deterministisk funktion. Repetitiva anrop returnerar `RAND` inte samma resultat.

## <a name="examples"></a>Exempel
  
  I följande exempel returneras ett slumpmässigt genererat numeriskt värde.
  
```sql
SELECT RAND() AS rand 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
