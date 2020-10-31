---
title: GetCurrentTicks i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function GetCurrentTicks i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 4841c374454dea9afdf0dbc4094311ded54cfbb6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098526"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Returnerar antalet 100-nanosekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Retur typer

Returnerar ett signerat numeriskt värde, det aktuella antalet 100-nanosekunder som har förflutit sedan UNIX-epoken. Med andra ord returnerar GetCurrentTicks antalet 100 nanosekunder som har förflutit sedan 00:00:00 torsdag 1 januari 1970.

## <a name="remarks"></a>Kommentarer

GetCurrentTicks () är en icke-deterministisk funktion. Resultatet som returneras är UTC (Coordinated Universal Time).

Den här system funktionen kommer inte att använda indexet.

## <a name="examples"></a>Exempel

Här är ett exempel som returnerar den aktuella tiden, mätt i Tick:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
