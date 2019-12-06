---
title: Mängd funktioner i Azure Cosmos DB
description: Lär dig mer om SQL agg regering Function-syntax, typer av mängd funktioner som stöds av Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 1ce3b18dd31944a1a4d4e6fad8fb49e63996dace
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871847"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Mängd funktioner i Azure Cosmos DB

Mängd funktioner utför en beräkning av en uppsättning värden i SELECT-satsen och returnerar ett enda värde. Följande fråga returnerar till exempel antalet objekt i `Families` container:

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

Du kan också kombinera agg regeringar med filter. Följande fråga returnerar till exempel antalet objekt med adress läget för `WA`.

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

SQL-API: et stöder följande mängd funktioner. SUM och AVG fungerar på numeriska värden och antal, MIN och MAX arbetet med siffror, strängar, booleska värden och null-värden.

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

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [System funktioner](sql-query-system-functions.md)
- [Användardefinierade funktioner](sql-query-udfs.md)