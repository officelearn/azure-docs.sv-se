---
title: Mängd funktioner i Azure Cosmos DB
description: Lär dig mer om SQL agg regering Function-syntax, typer av mängd funktioner som stöds av Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 8d71b9b888c47847dd4a5f5c40504190e5c1ec84
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090944"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Mängd funktioner i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Mängd funktioner utför en beräkning av en uppsättning värden i `SELECT` satsen och returnerar ett enda värde. Följande fråga returnerar till exempel antalet objekt i `Families` behållaren:

## <a name="examples"></a>Exempel

```sql
    SELECT COUNT(1)
    FROM Families f
```

Resultatet är:

```json
    [{
        "$1": 2
    }]
```

Du kan också returnera det skalära värdet för agg regeringen genom att använda nyckelordet VALUE. Till exempel returnerar följande fråga antalet värden som ett enskilt tal:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Resultatet är:

```json
    [ 2 ]
```

Du kan också kombinera agg regeringar med filter. Följande fråga returnerar till exempel antalet objekt med adress statusen för `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Resultatet är:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typer av mängd funktioner

SQL-API: et stöder följande mängd funktioner. `SUM` och `AVG` använder numeriska värden, och `COUNT` , `MIN` , och `MAX` arbetar med siffror, strängar, booleska värden och null-värden.

| Funktion | Beskrivning |
|-------|-------------|
| COUNT | Returnerar antalet objekt i uttrycket. |
| SUM   | Returnerar summan av alla värden i uttrycket. |
| MIN   | Returnerar minimivärdet i uttrycket. |
| MAX   | Returnerar maxvärdet i uttrycket. |
| AVG   | Returnerar medelvärdet av värdena i uttrycket. |

Du kan också aggregera över resultatet av en mat ris iteration.

> [!NOTE]
> I Azure Portals Datautforskaren kan agg regerings frågor aggregera delar av resultaten över bara en fråge sida. SDK skapar ett enda ackumulerat värde för alla sidor. Om du vill utföra agg regerings frågor med hjälp av kod behöver du .NET SDK-1.12.0, .NET Core SDK 1.1.0 eller Java SDK 1.9.5 eller senare.

## <a name="remarks"></a>Kommentarer

Dessa sammanställda system funktioner kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy). Om du förväntar dig att göra ett,,, `COUNT` `SUM` `MIN` `MAX` eller `AVG` en egenskap, bör du [ta med relevant sökväg i indexerings principen](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Systemfunktioner](sql-query-system-functions.md)
- [Användardefinierade funktioner](sql-query-udfs.md)