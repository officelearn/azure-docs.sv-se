---
title: SQL JOIN-frågor för Azure Cosmos DB
description: Lär dig hur du kopplar ihop flera tabeller i Azure Cosmos DB för att fråga data
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871150"
---
# <a name="joins-in-azure-cosmos-db"></a>Kopplingar i Azure Cosmos DB

I en Relations databas är sammanfogningar över tabeller den logiska överrullningen för att utforma normaliserade scheman. SQL-API: t använder däremot den avnormaliserade data modellen för schema fria objekt, vilket är den logiska motsvarigheten till en *själv koppling*.

Inre kopplingar resultera i en fullständig kryssprodukten av de mängder som deltar i kopplingen. Resultatet av en N-vägs-koppling är en uppsättning element N tupplar, där varje värde i tuppeln är associerad med alias som deltar i kopplingen och kan nås genom att referera till som alias i andra-satser.

## <a name="syntax"></a>Syntax

Språket stöder syntaxen `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Den här frågan returnerar en uppsättning tupler med `N` värden. Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar. 

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

I följande exempel visas hur JOIN-satsen fungerar. Innan du kör de här exemplen laddar du upp exempel [familjens data](sql-query-getting-started.md#upload-sample-data). I följande exempel är resultatet tomt, eftersom kryss produkten för varje objekt från källa och en tom uppsättning är tom:

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

I följande exempel är kopplingen en mellan produkt mellan två JSON-objekt, objekt roten `id` och `children`-underroten. Det faktum att `children` är en matris inte är effektiv i kopplingen, eftersom den behandlar en enda rot som är `children` matrisen. Resultatet innehåller bara två resultat eftersom kryss produkten för varje objekt med matrisen ger exakt bara ett objekt.

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

FROM-källa för JOIN-satsen är en iterator. Därför är flödet i föregående exempel:  

1. Expandera varje underordnat element `c` i matrisen.
2. Använd en kors produkt med roten för objektet `f` med varje underordnat element `c` som det första steget sammanslagna.
3. Slutligen kan du projicera rotobjektet `f` `id` egenskap ensamt.

Det första objektet `AndersenFamily`innehåller bara ett `children`-element, så resultat uppsättningen innehåller bara ett enda objekt. Det andra objektet `WakefieldFamily`, innehåller två `children`, så att kors produkten genererar två objekt, ett för varje `children`-element. Rotfälten i båda dessa objekt är detsamma, vilket är det som förväntas i en kryssprodukt.

Det verkliga verktyget i JOIN-satsen är att forma tupler från kors produkten i en form som annars är svår att projicera. I exemplet nedan filtreras kombinationen av en tupel som gör det möjligt för användaren att välja ett villkor som är uppfyllt av tupelarna generellt.

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

Följande tillägg i föregående exempel utför en dubbel koppling. Du kan visa kors produkten som följande pseudo-kod:

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

`AndersenFamily` har en underordnad som har ett sällskaps djur, så att kors produkten ger en rad (1\*1\*1) från den här familjen. `WakefieldFamily` har två underordnade, endast en av vem som har hus djur, men den underordnade har två hus djur. Den här seriens kors produkt ger 1\*1\*2 = 2 rader.

I nästa exempel finns ett ytterligare filter på `pet`, vilket utesluter alla tupler där PET-namnet inte är `Shadow`. Du kan bygga tupler från matriser, filtrera efter alla element i tuppeln och projicera en kombination av elementen.

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
- [Under frågor](sql-query-subquery.md)