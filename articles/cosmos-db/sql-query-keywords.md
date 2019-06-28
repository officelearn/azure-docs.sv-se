---
title: SQL-nyckelord för Azure Cosmos DB
description: Läs mer om SQL-nyckelord för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342680"
---
# <a name="keywords-in-azure-cosmos-db"></a>Nyckelord i Azure Cosmos DB
Den här artikeln beskriver nyckelord som kan användas i Azure Cosmos DB SQL-frågor.

## <a name="between"></a>MELLAN

Du kan använda nyckelordet BETWEEN för att uttrycka frågor mot cellområden sträng eller numeriska värden som ANSI SQL. Till exempel returnerar följande fråga alla objekt som den första underordnade i företagsklass är 1-5, inklusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Till skillnad från i ANSI SQL, kan du också använda BETWEEN-satsen i FROM-satsen, som i följande exempel.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Du kan ange intervallfrågor mot egenskaper av olika typer i SQL-API, till skillnad från ANSI SQL. Till exempel `grade` kan vara ett nummer som `5` i vissa objekt och liknande `grade4` i andra. I dessa fall, som i JavaScript, en jämförelse mellan de två olika typerna resulterar i `Undefined`, så att objektet har hoppats över.

> [!TIP]
> Skapa en indexprincip som använder en intervallet Indextyp mot alla egenskaper för numeriska eller sökvägar BETWEEN-satsen filtrerar för snabbare fråga körningstider.

## <a name="distinct"></a>DISTINKTA

Nyckelordet DISTINCT eliminerar dubbletter i frågans projektion.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

I det här exemplet genererar frågan värden för varje efternamn.

Resultatet är:

```json
[
    "Andersen"
]
```

Du kan också projicera unika objekt. Efternamn finns i det här fallet inte på något av två dokument, så att frågan returnerar ett tomt-objekt.

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

DISTINCT kan också användas i projektionen i en underfråga:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Den här frågan genererar en matris som innehåller varje underordnad givenName med borttagna dubbletter. Den här matrisen är ett alias för konsolkommandot ChildNames och planerade i yttre frågan.

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
## <a name="in"></a> INDIEN

Använda nyckelordet IN för att kontrollera om ett angivet värde matchar något värde i en lista. Till exempel följande fråga returnerar alla family objekt där den `id` är `WakefieldFamily` eller `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

I följande exempel returneras alla objekt där tillståndet är någon av de angivna värdena:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL-API har stöd för [iterera över JSON-matriser](sql-query-object-array.md#Iteration), med en ny konstruktion som lagts till via i nyckelordet i FROM-källan. 

## <a name="top"></a>LÄNGST UPP

Nyckelordet ÖVERSTA returnerar första `N` antal frågeresultaten i en odefinierad order. Ett bra tips är att använda TOP med ORDER BY-satsen för att begränsa resultaten till först `N` antal sorterad värden. Om du kombinerar dessa två satser är det enda sättet att förutsägbart indikerar vilka rader ÖVERSTA påverkar.

Du kan använda upp med ett konstant värde, som i följande exempel, eller med ett variabelvärde använda parameteriserade frågor.

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