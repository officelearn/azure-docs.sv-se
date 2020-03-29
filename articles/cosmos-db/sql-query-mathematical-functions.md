---
title: Matematiska funktioner i Azure Cosmos DB-frågespråk
description: Lär dig mer om de matematiska funktionerna i Azure Cosmos DB för att utföra en beräkning, baserat på indatavärden som tillhandahålls som argument, och returnera ett numeriskt värde.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873275"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Matematiska funktioner (Azure Cosmos DB)  

De matematiska funktionerna utför en beräkning baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde.

Du kan köra frågor som följande exempel:

```sql
    SELECT VALUE ABS(-4)
```

Resultatet är:

```json
    [4]
```

## <a name="functions"></a>Funktioner

Följande inbyggda matematiska funktioner som stöds utför en beräkning, vanligtvis baserat på indataargument, och returnerar ett numeriskt uttryck.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[Exp](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[Logga in](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[Runda](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[Synd](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[Square](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Alla matematiska funktioner, utom RAND, är deterministiska funktioner. Det innebär att de returnerar samma resultat varje gång de anropas med en viss uppsättning indatavärden.

## <a name="next-steps"></a>Nästa steg

- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
- [Användardefinierade funktioner](sql-query-udfs.md)
- [Samlingar](sql-query-aggregates.md)
