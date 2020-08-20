---
title: GetCurrentDateTime i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function GetCurrentDateTime i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ec0b8ccaceed4abe3dd2784463f507f3bc76d890
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606955"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)

Returnerar den aktuella UTC-tiden (Coordinated Universal Time) som en ISO 8601-sträng.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Retur typer
  
  Returnerar aktuellt UTC-datum och klock slags ISO 8601-sträng värde i formatet `YYYY-MM-DDThh:mm:ss.fffffffZ` där:
  
  |Format|Beskrivning|
  |-|-|
  |MMMM|fyrsiffrigt år|
  |MM|tvåsiffrig månad (01 = januari osv.)|
  |FORMATET|fyrsiffrigt dag i månad (01 till 31)|
  |T|indikerare för start av tids element|
  |hh|Dubbels siffrig timme (00 – 23)|
  |mm|två-siffriga minuter (00 till 59)|
  |ss|tvåsiffriga sekunder (00 till 59)|
  |.fffffff|sju siffrors decimal sekunder|
  |Z|UTC-beteckning (Coordinated Universal Time)||
  
  Mer information om ISO 8601-formatet finns i [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Kommentarer

GetCurrentDateTime () är en icke-deterministisk funktion. Resultatet som returneras är UTC. Precision är 7 siffror, med en noggrannhet på 100 nanosekunder.

Den här system funktionen kommer inte att använda indexet.

## <a name="examples"></a>Exempel
  
I följande exempel visas hur du hämtar den aktuella UTC-datum tiden med den inbyggda funktionen GetCurrentDateTime ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Här är ett exempel på en resultat uppsättning.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
