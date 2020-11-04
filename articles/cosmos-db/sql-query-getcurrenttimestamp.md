---
title: GetCurrentTimestamp i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function GetCurrentTimestamp i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6720b0e5d13f2baaaf063fef2244b0c1f1863571
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341934"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar antalet millisekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Retur typer
  
Returnerar ett signerat numeriskt värde, det aktuella antalet millisekunder som har förflutit sedan UNIX-epoken, dvs. antalet millisekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970.

## <a name="remarks"></a>Kommentarer

GetCurrentTimestamp () är en icke-deterministisk funktion. Resultatet som returneras är UTC (Coordinated Universal Time).

Den här system funktionen kommer inte att använda indexet.

## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du hämtar den aktuella tidsstämpeln med hjälp av den inbyggda funktionen GetCurrentTimestamp ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Här är ett exempel på en resultat uppsättning.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
