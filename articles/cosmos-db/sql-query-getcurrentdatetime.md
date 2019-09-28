---
title: GetCurrentDateTime i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function GetCurrentDateTime i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8a2c3dcd3c8ca6dc9d751e50a7862fe98e6de510
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351018"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Returnerar den aktuella UTC-tiden (Coordinated Universal Time) som en ISO 8601-sträng.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Retur typer
  
  Returnerar aktuellt UTC-datum och klock slags ISO 8601-sträng värde i formatet `YYYY-MM-DDThh:mm:ss.sssZ` där:
  
  |||
  |-|-|
  |MMMM|fyrsiffrigt år|
  |MM|tvåsiffrig månad (01 = januari osv.)|
  |DD|fyrsiffrigt dag i månad (01 till 31)|
  |t|indikerare för start av tids element|
  |formatet|två siffror per timme (00 – 23)|
  |hög|två siffer minuter (00 till 59)|
  |ss|två siffror i sekunder (00 till 59)|
  |.sss|tre siffror på Decimal bråk i en sekund|
  |Z|UTC-beteckning (Coordinated Universal Time)||
  
  Mer information om ISO 8601-formatet finns i [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Kommentarer

  GetCurrentDateTime () är en icke-deterministisk funktion. 
  
  Resultatet som returneras är UTC.

## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du hämtar den aktuella UTC-datum tiden med den inbyggda funktionen GetCurrentDateTime ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Här är ett exempel på en resultat uppsättning.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
