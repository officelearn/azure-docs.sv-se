---
title: GetCurrentTimestamp i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen GetCurrentTimestamp i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351005"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Returnerar antalet millisekunder som har förflutit sedan 00:00:00 torsdagen den 1 januari 1970. 
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt värde, det aktuella antalet millisekunder som har förflutit sedan Unix-epoken, dvs.

## <a name="remarks"></a>Anmärkningar

  GetCurrentTimestamp() är en icke-deterministisk funktion.
  
  Resultatet som returneras är UTC (Coordinated Universal Time).

## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du hämtar den aktuella tidsstämpeln med den inbyggda funktionen GetCurrentTimestamp().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Här är ett exempel resultatuppsättning.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Nästa steg

- [Datum- och tidsfunktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
