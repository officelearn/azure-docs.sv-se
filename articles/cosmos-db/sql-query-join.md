---
title: Ansluta till SQL-frågor för Azure Cosmos DB
description: Läs mer om ansluta till SQL-syntax för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342885"
---
# <a name="joins-in-azure-cosmos-db"></a>Ansluter till i Azure Cosmos DB

Kopplingar mellan tabeller finns i en relationsdatabas, den logiska naturlig följd att utforma normaliserade scheman. Däremot SQL API använder Avnormaliserade datamodellen schemafria objekt som är den logiska motsvarar en *självkoppling*.

Inre kopplingar resultera i en fullständig kryssprodukten av de mängder som deltar i kopplingen. Resultatet av en N-vägs-koppling är en uppsättning element N tupplar, där varje värde i tuppeln är associerad med alias som deltar i kopplingen och kan nås genom att referera till som alias i andra-satser.

## <a name="syntax"></a>Syntax

Språket stöder syntaxen `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Den här frågan returnerar en uppsättning tupplar med `N` värden. Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar. 

Låt oss titta på följande FROM-satsen: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Låt varje källa som definierar `input_alias1, input_alias2, …, input_aliasN`. Den här FROM-satsen returnerar en mängd av N-tupplar (tuppel med N värden). Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar.  
  
**Exempel 1** -2 källor  
  
- Låt `<from_source1>` container-begränsas och som representerar uppsättningen {A, B, C}.  
  
- Låt `<from_source2>` vara dokument-omfattande refererar till input_alias1 och representerar uppsättningar:  
  
    {1, 2} för `input_alias1 = A,`  
  
    {3} för `input_alias1 = B,`  
  
    {4, 5} för `input_alias1 = C,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2>` resulterar i följande tupplar:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exempel 2** -3 källor  
  
- Låt `<from_source1>` container-begränsas och som representerar uppsättningen {A, B, C}.  
  
- Låt `<from_source2>` vara begränsade av dokumentet refererar till `input_alias1` och representerar uppsättningar:  
  
    {1, 2} för `input_alias1 = A,`  
  
    {3} för `input_alias1 = B,`  
  
    {4, 5} för `input_alias1 = C,`  
  
- Låt `<from_source3>` vara begränsade av dokumentet refererar till `input_alias2` och representerar uppsättningar:  
  
    {100, 200} för `input_alias2 = 1,`  
  
    {300} för `input_alias2 = 3,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2> JOIN <from_source3>` resulterar i följande tupplar:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Brist på tupplar för andra värden på `input_alias1`, `input_alias2`, som den `<from_source3>` returnerade inte några värden.  
  
**Exempel 3** -3 källor  
  
- Låt < from_source1 > vara container-omfattande och som representerar uppsättningen {A, B, C}.  
  
- Låt `<from_source1>` container-begränsas och som representerar uppsättningen {A, B, C}.  
  
- Låt < from_source2 > vara dokument-omfattande refererande input_alias1 och representerar uppsättningar:  
  
    {1, 2} för `input_alias1 = A,`  
  
    {3} för `input_alias1 = B,`  
  
    {4, 5} för `input_alias1 = C,`  
  
- Låt `<from_source3>` begränsas till `input_alias1` och representerar uppsättningar:  
  
    {100, 200} för `input_alias2 = A,`  
  
    {300} för `input_alias2 = C,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2> JOIN <from_source3>` resulterar i följande tupplar:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), C, 4, 300, (C, 5, 300)  
  
  > [!NOTE]
  > Detta resulterade i kryssprodukten mellan `<from_source2>` och `<from_source3>` eftersom båda är begränsade till samma `<from_source1>`.  Detta resulterade i 4 (2 × 2) tupplar som har värdet A, 0 tupplar som har värdet B (1 x 0) och 2 (2 x 1) tupplar som har värdet C.  
  
## <a name="examples"></a>Exempel

I följande exempel visas hur JOIN-satsen fungerar. Resultatet är tom, sedan kryssprodukten av varje objekt från källa och en tom uppsättning är tom i exemplet nedan:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Resultatet är:

```json
    [{
    }]
```

I följande exempel i kopplingen är en kryssprodukten mellan två JSON-objekt, objekt-roten `id` och `children` subroot. Faktumet som `children` är en matris inte är effektivt i kopplingen, eftersom det handlar om en enda rot som är den `children` matris. Resultatet innehåller bara två resultat, eftersom kryssprodukten av varje objekt med matrisen ger exakt endast ett objekt.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Resultatet är:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

I följande exempel visas en mer konventionell koppling:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Resultatet är:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN-satsen FROM orsaken är ett Iteratorn. Därför är flödet i föregående exempel:  

1. Expandera varje underordnat element `c` i matrisen.
2. Tillämpa en kryssprodukten med rot objektets `f` med varje underordnat element `c` som det första steget tillplattad.
3. Slutligen projektet rotobjektet `f` `id` egenskapen enbart.

Det första objektet `AndersenFamily`, innehåller endast en `children` element, så resultatet innehåller bara ett enda objekt. Det andra objektet `WakefieldFamily`, innehåller två `children`, så kryssprodukten skapar två objekt, en för varje `children` element. Rotfälten i båda dessa objekt är detsamma, vilket är det som förväntas i en kryssprodukt.

Verkliga nyttan av JOIN-satsen är att formuläret tupplar från kryssprodukten i en form som annars är svåra att projektet. Exemplet nedan filter på en kombination av en tuppel som låter användaren välja ett villkor som är nöjd med den övergripande tupplar.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Resultatet är:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Följande tillägg i föregående exempel skapar en dubbla koppling. Du kan visa kryssprodukten som genererat följande kod:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` har en underordnad som har en husdjur så kryssprodukten ger en rad (1\*1\*1) från den här serien. `WakefieldFamily` har två barn, endast en av som har husdjur, men den underordnat har två husdjur. Kryssprodukten för den här serien ger 1\*1\*2 = 2 rader.

I nästa exempel finns ett ytterligare filter på `pet`, vilket utesluter alla tupplar där husdjur namnet inte är `Shadow`. Du kan skapa tupplar från matriser, filter på någon av elementen i tuppeln och projicera olika kombinationer av elementen.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Resultatet är:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Underfrågor](sql-query-subquery.md)