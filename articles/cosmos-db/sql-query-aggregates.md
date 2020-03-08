---
title: Mängd funktioner i Azure Cosmos DB
description: Lär dig mer om SQL agg regering Function-syntax, typer av mängd funktioner som stöds av Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: tisande
ms.openlocfilehash: df9700dd51c8915ff28c34cf0a29c2f5e48baa44
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897836"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Mängd funktioner i Azure Cosmos DB

Mängd funktioner utför en beräkning av en uppsättning värden i SELECT-satsen och returnerar ett enda värde. Följande fråga returnerar till exempel antalet objekt i `Families` container:

## <a name="examples"></a>Exempel

```sql
    SELECT COUNT(1)
    FROM Families f
```

Resultaten är:

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

Resultaten är:

```json
    [ 2 ]
```

Du kan också kombinera agg regeringar med filter. Följande fråga returnerar till exempel antalet objekt med adress läget för `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Resultaten är:

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