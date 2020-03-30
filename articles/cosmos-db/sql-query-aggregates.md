---
title: Mängdfunktioner i Azure Cosmos DB
description: Lär dig mer om SQL-mängdfunktionssyntax, typer av mängdfunktioner som stöds av Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464469"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Mängdfunktioner i Azure Cosmos DB

Mängdfunktioner utför en beräkning på en `SELECT` uppsättning värden i satsen och returnerar ett enda värde. Följande fråga returnerar till exempel antalet `Families` objekt i behållaren:

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

Du kan också returnera endast skalvärdet för aggregerade med hjälp av nyckelordet VALUE. Till exempel returnerar följande fråga antalet värden som ett enskilt tal:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Resultatet är:

```json
    [ 2 ]
```

Du kan också kombinera aggregeringar med filter. Följande fråga returnerar till exempel antalet objekt med `WA`adresstillståndet för .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Resultatet är:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typer av aggregerade funktioner

SQL API stöder följande mängdfunktioner. `SUM`och `AVG` arbetar med numeriska `COUNT` `MIN`värden `MAX` och , och arbetar med tal, strängar, booleaner och null.

| Funktion | Beskrivning |
|-------|-------------|
| COUNT | Returnerar antalet objekt i uttrycket. |
| SUM   | Returnerar summan av alla värden i uttrycket. |
| MIN   | Returnerar minimivärdet i uttrycket. |
| MAX   | Returnerar maxvärdet i uttrycket. |
| AVG   | Returnerar medelvärdet av värdena i uttrycket. |

Du kan också aggregera över resultatet av en matrisiteration.

> [!NOTE]
> I Azure-portalens datautforskaren kan aggregeringsfrågor aggregera partiella resultat över endast en frågesida. SDK ger ett enda ackumulerat värde på alla sidor. Om du vill utföra aggregeringsfrågor med hjälp av kod behöver du .NET SDK 1.12.0, .NET Core SDK 1.1.0 eller Java SDK 1.9.5 eller högre.

## <a name="remarks"></a>Anmärkningar

Dessa aggregerade systemfunktioner kommer att dra nytta av ett [intervallindex](index-policy.md#includeexclude-strategy). Om du förväntar `COUNT`dig `SUM` `MIN`att `MAX`göra `AVG` en , , , eller på en egenskap, bör du [inkludera den relevanta sökvägen i indexeringsprincipen](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Systemfunktioner](sql-query-system-functions.md)
- [Användardefinierade funktioner](sql-query-udfs.md)