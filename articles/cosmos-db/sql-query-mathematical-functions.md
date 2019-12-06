---
title: Matematiska funktioner i Azure Cosmos DB frågespråk
description: Lär dig mer om de matematiska funktionerna i Azure Cosmos DB att utföra en beräkning, baserat på indatavärden som har angetts som argument och returnera ett numeriskt värde.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873275"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Matematiska funktioner (Azure Cosmos DB)  

De matematiska funktionerna utför en beräkning baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde.

Du kan köra frågor som i följande exempel:

```sql
    SELECT VALUE ABS(-4)
```

Resultatet är:

```json
    [4]
```

## <a name="functions"></a>Functions

Följande inbyggda matematiska funktioner som stöds utför en beräkning, vanligt vis baserat på indataargument, och returnerar ett numeriskt uttryck.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[TAK](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[GRADER](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[VÅNING](sql-query-floor.md)|[LOG](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANER](sql-query-radians.md)|[RAND](sql-query-rand.md)|[AVRUNDA](sql-query-round.md)|
|[INLOGGNING](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[RUTA](sql-query-square.md)|[TAN](sql-query-tan.md)|[AVKORTA](sql-query-trunc.md)||  
  
Alla matematiska funktioner, förutom slump, är deterministiska funktioner. Det innebär att de returnerar samma resultat varje gång de anropas med en angiven uppsättning indatavärden.

## <a name="next-steps"></a>Nästa steg

- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
- [Användardefinierade funktioner](sql-query-udfs.md)
- [Aggregeringar](sql-query-aggregates.md)
