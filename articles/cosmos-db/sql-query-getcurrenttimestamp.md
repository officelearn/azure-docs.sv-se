---
title: GetCurrentTimestamp i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function GetCurrentTimestamp i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "71351005"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Returnerar antalet millisekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970. 
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt värde, det aktuella antalet millisekunder som har förflutit sedan UNIX-epoken, dvs. antalet millisekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970.

## <a name="remarks"></a>Kommentarer

  GetCurrentTimestamp () är en icke-deterministisk funktion.
  
  Resultatet som returneras är UTC (Coordinated Universal Time).

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
