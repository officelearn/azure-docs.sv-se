---
title: Mängdfunktioner i Azure Cosmos DB
description: Läs mer om SQL-syntax för mängdfunktionen för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342888"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Mängdfunktioner i Azure Cosmos DB

Mängdfunktioner utföra beräkningar på en uppsättning värden i SELECT-satsen och returnera ett enstaka värde. Till exempel följande fråga returnerar antalet objekt i den `Families` behållare:

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

Du kan också returnera endast skalära värdet för mängden med hjälp av nyckelordet värde. Till exempel returnerar följande fråga antalet värden som ett enskilt tal:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Resultatet är:

```json
    [ 2 ]
```

Du kan också kombinera aggregeringar med filter. Till exempel följande fråga returnerar antalet objekt med adressen tillstånd `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Resultatet är:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typer av mängdfunktioner

SQL-API: et stöder mängdfunktionerna. Summa och Genomsnittlig fungerar på numeriska värden och COUNT, MIN och MAX fungerar på tal, strängar, booleska värden och null-värden.

| Funktion | Beskrivning |
|-------|-------------|
| COUNT | Returnerar antalet objekt i uttrycket. |
| SUM   | Returnerar summan av alla värden i uttrycket. |
| MIN   | Returnerar minimivärdet i uttrycket. |
| MAX   | Returnerar maxvärdet i uttrycket. |
| AVG   | Returnerar medelvärdet av värdena i uttrycket. |

Du kan även aggregera över resultatet av en matris iteration.

> [!NOTE]
> I Datautforskaren i Azure portal, kan mängdfrågor aggregera ofullständiga resultat via sidan för enbart en fråga. SDK: N producerar en enda ackumulerade värdet på alla sidor. Om du vill utföra mängdfrågor med hjälp av kod du behöver .NET SDK 1.12.0, .NET Core SDK 1.1.0 eller Java SDK 1.9.5 eller senare.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Systemfunktioner](sql-query-system-functions.md)
- [Användardefinierade funktioner](sql-query-udfs.md)