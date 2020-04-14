---
title: SQL-nyckelord för Azure Cosmos DB
description: Läs mer om SQL-nyckelord för Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 069548b9b69ef6f7f6bde85ede830d97f3d312db
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261575"
---
# <a name="keywords-in-azure-cosmos-db"></a>Nyckelord i Azure Cosmos DB

I den här artikeln beskrivs nyckelord som kan användas i Azure Cosmos DB SQL-frågor.

## <a name="between"></a>BETWEEN

Du kan `BETWEEN` använda nyckelordet för att uttrycka frågor mot intervall med sträng- eller numeriska värden. Följande fråga returnerar till exempel alla objekt där det första barnets resultat är 1-5, inklusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Du kan också `BETWEEN` använda `SELECT` nyckelordet i satsen, som i följande exempel.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

I SQL API, till skillnad från ANSI SQL, kan du uttrycka intervallfrågor mot egenskaper för blandade typer. Det kan `grade` till exempel `5` vara ett tal som `grade4` i vissa objekt och en sträng som i andra. I dessa fall, som i JavaScript, resulterar `Undefined`jämförelsen mellan de två olika typerna i , så objektet hoppas över.

> [!TIP]
> För snabbare körningstider för frågor skapar du en indexeringsprincip som använder `BETWEEN` en intervallindextyp mot alla numeriska egenskaper eller banor som satsen filtrerar.

## <a name="distinct"></a>DISTINKTA

Nyckelordet `DISTINCT` eliminerar dubbletter i frågans projektion.

I det här exemplet projicerar frågan värden för varje efternamn:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Resultatet är:

```json
[
    "Andersen"
]
```

Du kan också projicera unika objekt. I det här fallet finns inte fältet efternamn i något av de två dokumenten, så frågan returnerar ett tomt objekt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Resultatet är:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT kan också användas i projektionen inom en underkräpelse:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Den här frågan projicerar en matris som innehåller varje underordnads givenName med dubbletter borttagna. Den här matrisen är aliaserad som ChildNames och projiceras i den yttre frågan.

Resultatet är:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Frågor med en mängdsystemfunktion och en `DISTINCT` underkvent med stöds inte. Följande fråga stöds till exempel inte:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Använd nyckelordet IN för att kontrollera om ett angivet värde matchar något värde i en lista. Följande fråga returnerar till exempel alla `id` `WakefieldFamily` familjeobjekt där den finns eller `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

I följande exempel returneras alla objekt där tillståndet är något av de angivna värdena:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API ger stöd för [itererering över JSON-matriser](sql-query-object-array.md#Iteration), med en ny konstruktion som lagts till via nyckelordet in i FROM-källan.

Om du inkluderar partitionsnyckeln i filtret `IN` filtreras frågan automatiskt till endast relevanta partitioner.

## <a name="top"></a>Topp

Nyckelordet TOP returnerar det första `N` antalet frågeresultat i en odefinierad ordning. Bäst i övning kan du `ORDER BY` använda TOP med `N` satsen för att begränsa resultaten till det första antalet beställda värden. Att kombinera dessa två satser är det enda sättet att förutsägbart ange vilka rader TOP påverkar.

Du kan använda TOP med ett konstant värde, som i följande exempel, eller med ett variabelvärde med hjälp av parameteriserade frågor.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Resultatet är:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Kopplingar](sql-query-join.md)
- [Underfrågor](sql-query-subquery.md)