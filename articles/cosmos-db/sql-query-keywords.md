---
title: SQL-nyckelord för Azure Cosmos DB
description: Läs om SQL-nyckelord för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 711e961bd5eb1607e2e6f11b0b5762423d78c0e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246583"
---
# <a name="keywords-in-azure-cosmos-db"></a>Nyckelord i Azure Cosmos DB
Den här artikeln innehåller information om nyckelord som kan användas i Azure Cosmos DB SQL-frågor.

## <a name="between"></a>DELAS

Som i ANSI SQL kan du använda nyckelordet BETWEEN för att uttrycka frågor mot intervall med sträng eller numeriska värden. Följande fråga returnerar till exempel alla objekt där de första underordnade klasserna är 1-5.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Till skillnad från ANSI SQL kan du också använda uttrycket BETWEEN i from-satsen, som i följande exempel.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

I SQL API, till skillnad från ANSI SQL, kan du uttrycka intervall frågor mot egenskaper av blandade typer. `grade` kan till exempel vara ett tal som `5` i vissa objekt och en sträng som `grade4` i andra. I dessa fall, som i Java Script, resulterar jämförelsen mellan de två olika typerna i `Undefined`, så att objektet hoppas över.

> [!TIP]
> För snabbare körnings tider för frågor skapar du en indexerings princip som använder en intervall index typ mot eventuella numeriska egenskaper eller sökvägar som BETWEEN-satsen filtrerar.

## <a name="distinct"></a>KONTROLLSTÄMPEL

Nyckelordet DISTINCT eliminerar dubbletter i frågans projektion.

I det här exemplet är fråga projekt värden för varje efter namn:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Resultaten är:

```json
[
    "Andersen"
]
```

Du kan också projicera unika objekt. I det här fallet finns inte fältet lastName i något av de två dokumenten, så frågan returnerar ett tomt objekt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Resultaten är:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT kan också användas i projektionen inom en under fråga:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Den här frågan projekterar en matris som innehåller varje barns givenName och dubbletter tas bort. Matrisen har ett alias som ChildNames och projiceras i den yttre frågan.

Resultaten är:

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

Frågor med en sammansatt systemfunktion och en under fråga med DISTINCT stöds inte. Följande fråga stöds exempelvis inte:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Använd nyckelordet IN för att kontrol lera om ett angivet värde matchar ett värde i en lista. Följande fråga returnerar till exempel alla familje objekt där `id` är `WakefieldFamily` eller `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

I följande exempel returneras alla objekt där status är något av de angivna värdena:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API ger stöd för att [iterera över JSON-matriser](sql-query-object-array.md#Iteration), med en ny konstruktion som lagts till via nyckelordet i från-källan.

Om du inkluderar din partitionsnyckel i `IN`s filtret filtreras frågan automatiskt till de relevanta partitionerna.

## <a name="top"></a>ÖVERSTA

Nyckelordet TOP returnerar det första `N` antalet frågeresultat i en odefinierad ordning. Bästa praxis är att använda TOP med ORDER BY-satsen för att begränsa resultaten till det första `N` antalet beställda värden. Att kombinera dessa två satser är det enda sättet att förutsäga vilka rader som påverkar.

Du kan använda TOP med ett konstant värde, som i följande exempel, eller med ett variabel värde med hjälp av parametriserade frågor.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Resultaten är:

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
- [Under frågor](sql-query-subquery.md)