---
title: GetCurrentDateTime i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen GetCurrentDateTime i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303910"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Returnerar det aktuella UTC-datumet (Coordinated Universal Time) datum och tid som en ISO 8601-sträng.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Returtyper
  
  Returnerar det aktuella UTC-strängvärdet för UTC-datum och tid i det format `YYYY-MM-DDThh:mm:ss.fffffffZ` där:
  
  |||
  |-|-|
  |Åååå|fyrsiffrigt årtal|
  |MM|tvåsiffrig månad (01 = januari, etc.)|
  |Dd|tvåsiffrig dag i månaden (01 till 31)|
  |T|signifier för början av tidselement|
  |hh|tvåsiffrig timme (00 till 23)|
  |mm|tvåsiffriga minuter (00 till 59)|
  |ss|tvåsiffriga sekunder (00 till 59)|
  |.fffffff (fffffff)|sjusiffriga bråksekunder|
  |Z|UTC-designator (Coordinated Universal Time)||
  
  Mer information om ISO 8601-formatet finns [i ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Anmärkningar

  GetCurrentDateTime() är en icke-deterministisk funktion. 
  
  Resultatet som returneras är UTC.

  Precision är 7 siffror, med en noggrannhet på 100 nanoseconds.

## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du får den aktuella UTC-datumtiden med hjälp av den inbyggda funktionen GetCurrentDateTime().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Här är ett exempel resultatuppsättning.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Datum- och tidsfunktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
