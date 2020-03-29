---
title: SQL JOIN-frågor för Azure Cosmos DB
description: Lär dig hur du gör flera tabeller i Azure Cosmos DB för att fråga data
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871150"
---
# <a name="joins-in-azure-cosmos-db"></a>Går med i Azure Cosmos DB

I en relationsdatabas är kopplingar över tabeller den logiska konsekvensen av att utforma normaliserade scheman. SQL API använder däremot den denormaliserade datamodellen för schemafria objekt, vilket är den logiska motsvarigheten till en *självkoppling*.

Inre kopplingar resulterar i en komplett korsprodukt av de uppsättningar som deltar i kopplingen. Resultatet av en N-vägskoppling är en uppsättning N-elementupptupplar, där varje värde i tuppeln är associerat med den aliaserade uppsättningen som deltar i kopplingen och kan nås genom att referera till det aliaset i andra satser.

## <a name="syntax"></a>Syntax

Språket stöder syntaxen `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Den här frågan returnerar en `N` uppsättning tupplar med värden. Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar. 

Låt oss titta på följande FROM-klausul:`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Låt varje `input_alias1, input_alias2, …, input_aliasN`källa definiera . Den här FROM-satsen returnerar en uppsättning N-tupplar (tuppel med N-värden). Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar.  
  
**Exempel 1** - 2 källor  
  
- Låt `<from_source1>` behållaren-scoped och representera set {A, B, C}.  
  
- Låt `<from_source2>` dokumentomfattad referera input_alias1 och representera uppsättningar:  
  
    {1, 2} för`input_alias1 = A,`  
  
    {3}För`input_alias1 = B,`  
  
    {4, 5} för`input_alias1 = C,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2>` resulterar i följande tupplar:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exempel 2** - 3 källor  
  
- Låt `<from_source1>` behållaren-scoped och representera set {A, B, C}.  
  
- Låt `<from_source2>` dokumentomfattad referera och `input_alias1` representera uppsättningar:  
  
    {1, 2} för`input_alias1 = A,`  
  
    {3}För`input_alias1 = B,`  
  
    {4, 5} för`input_alias1 = C,`  
  
- Låt `<from_source3>` dokumentomfattad referera och `input_alias2` representera uppsättningar:  
  
    {100, 200} för`input_alias2 = 1,`  
  
    {300}För`input_alias2 = 3,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2> JOIN <from_source3>` resulterar i följande tupplar:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Brist på tupplar för `input_alias1` `input_alias2`andra värden `<from_source3>` i , , för vilka inte returnera några värden.  
  
**Exempel 3** - 3 källor  
  
- Låt <from_source1> vara behållarbest och representera uppsättningen {A, B, C}.  
  
- Låt `<from_source1>` behållaren-scoped och representera set {A, B, C}.  
  
- Låt <from_source2> referera input_alias1 input_alias1 och representera uppsättningar:  
  
    {1, 2} för`input_alias1 = A,`  
  
    {3}För`input_alias1 = B,`  
  
    {4, 5} för`input_alias1 = C,`  
  
- Låt `<from_source3>` dig begränsas till `input_alias1` och representera uppsättningar:  
  
    {100, 200} för`input_alias2 = A,`  
  
    {300}För`input_alias2 = C,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2> JOIN <from_source3>` resulterar i följande tupplar:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300) , (C, 5, 300)  
  
  > [!NOTE]
  > Detta resulterade i `<from_source2>` en `<from_source3>` korsningsprodukt mellan `<from_source1>`och eftersom båda är begränsade till samma .  Detta resulterade i att 4 (2x2) tupplar hade värde A, 0 tupplar med värde B (1x0) och 2 (2x1) tupplar med värde C.  
  
## <a name="examples"></a>Exempel

I följande exempel visas hur JOIN-satsen fungerar. Innan du kör de här exemplen laddar du upp [exempelinformationen för familjen](sql-query-getting-started.md#upload-sample-data). I följande exempel är resultatet tomt eftersom korsprodukten för varje artikel från källan och en tom uppsättning är tom:

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

I följande exempel är kopplingen en korsprodukt mellan två JSON-objekt, artikelroten `id` och underroten. `children` Det faktum `children` att det är en matris är inte effektivt i kopplingen, `children` eftersom det handlar om en enda rot som är matrisen. Resultatet innehåller bara två resultat, eftersom korsprodukten för varje artikel med matrisen ger exakt bara en artikel.

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

FRÅN-källan till JOIN-satsen är en iterator. Så flödet i föregående exempel är:  

1. Expandera varje `c` underordnat element i matrisen.
2. Använd en korsprodukt med roten på `f` `c` artikeln med varje underordnat element som det första steget förenklade.
3. Slutligen projicerar du `f` `id` egenskapen root object ensam.

Det första `AndersenFamily`objektet innehåller bara `children` ett element, så resultatuppsättningen innehåller bara ett enda objekt. Den andra `WakefieldFamily`artikeln innehåller `children`två , så att korsprodukten producerar `children` två objekt, ett för varje element. Rotfälten i båda dessa objekt är detsamma, vilket är det som förväntas i en kryssprodukt.

Den verkliga nyttan med JOIN-satsen är att bilda tupplar från korsprodukten i en form som annars är svår att projicera. Exemplet nedan filtrerar på kombinationen av en tuppel som låter användaren välja ett villkor som uppfylls av tupplar totalt.

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

Följande tillägg i föregående exempel utför en dubbel koppling. Du kan visa korsprodukten som följande pseudokod:

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

`AndersenFamily`har ett barn som har ett husdjur, så den\*arga\*produkten ger en rad (1 1 1) från denna familj. `WakefieldFamily`har två barn, varav bara ett har husdjur, men barnet har två husdjur. Korsprodukten för denna familj ger\*1 1\*2 = 2 rader.

I nästa exempel finns det ytterligare `pet`ett filter på , som utesluter alla `Shadow`tupplar där husdjursnamnet inte är . Du kan skapa tupplar från matriser, filtrera på något av elementen i tuppeln och projicera valfri kombination av elementen.

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