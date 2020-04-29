---
title: Mängd funktioner i Azure Cosmos DB
description: Lär dig mer om SQL agg regering Function-syntax, typer av mängd funktioner som stöds av Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79464469"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Mängd funktioner i Azure Cosmos DB

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

Du kan också kombinera agg regeringar med filter. Följande fråga returnerar till exempel antalet objekt med adress statusen för `WA`.

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

SQL-API: et stöder följande mängd funktioner. `SUM`och `AVG` använder numeriska värden, och `COUNT`, `MIN`, och `MAX` arbetar med siffror, strängar, booleska värden och null-värden.

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

## <a name="remarks"></a>Anmärkningar

Dessa sammanställda system funktioner kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy). Om du förväntar dig `COUNT`att `SUM`göra `MIN`ett `MAX`,, `AVG` , eller en egenskap, bör du [ta med relevant sökväg i indexerings principen](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Systemfunktioner](sql-query-system-functions.md)
- [Användardefinierade funktioner](sql-query-udfs.md)