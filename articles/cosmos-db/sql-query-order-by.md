---
title: ORDER BY-satsen i Azure Cosmos DB
description: Läs mer om SQL ORDER BY-satsen för Azure Cosmos DB. Använd SQL som ett Azure Cosmos DB JSON-frågespråk.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: tisande
ms.openlocfilehash: 70702ee4a77e8b3c46de4354f3394bca4080d837
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641398"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY-satsen i Azure Cosmos DB

Den `ORDER BY` valfria satsen anger sorteringsordningen för resultat som returneras av frågan.

## <a name="syntax"></a>Syntax
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argument
  
- `<sort_specification>`  
  
   Anger en egenskap eller ett uttryck som frågeresultatuppsättningen ska sorteras på. En sorteringskolumn kan anges som ett namn eller egenskapsalias.  
  
   Flera egenskaper kan anges. Egenskapsnamn måste vara unika. Sekvensen för sorteringsegenskaperna `ORDER BY` i satsen definierar organisationen för den sorterade resultatuppsättningen. Det innebär att resultatuppsättningen sorteras efter den första egenskapen och sedan sorteras den ordnade listan efter den andra egenskapen och så vidare.  
  
   De egenskapsnamn som `ORDER BY` refereras i satsen måste motsvara antingen en egenskap i urvalslistan eller till en egenskap som definieras i samlingen som anges i `FROM` satsen utan några oklarheter.  
  
- `<sort_expression>`  
  
   Anger en eller flera egenskaper eller uttryck som frågeresultatuppsättningen ska sorteras på.  
  
- `<scalar_expression>`  
  
   Mer information finns i avsnittet [Skaläruttryck.](sql-query-scalar-expressions.md)  
  
- `ASC | DESC`  
  
   Anger att värdena i den angivna kolumnen ska sorteras i stigande eller fallande ordning. `ASC`från det lägsta värdet till det högsta värdet. `DESC`från högsta till lägsta värde. `ASC`är standardsorteringsordningen. Null-värden behandlas som lägsta möjliga värden.  
  
## <a name="remarks"></a>Anmärkningar  
  
   Satsen `ORDER BY` kräver att indexeringsprincipen innehåller ett index för de fält som sorteras. Azure Cosmos DB-frågekörningen stöder sortering mot ett egenskapsnamn och inte mot beräknade egenskaper. Azure Cosmos DB `ORDER BY` stöder flera egenskaper. Om du vill köra en fråga med flera ORDER BY-egenskaper bör du definiera ett [sammansatt index](index-policy.md#composite-indexes) för de fält som sorteras.

> [!Note]
> Om de egenskaper som sorteras kan vara odefinierade för vissa dokument och du vill hämta dem i en ORDER BY-fråga, måste du uttryckligen inkludera den här sökvägen i indexet. Standardindexeringsprincipen tillåter inte hämtning av de dokument där sorteringsegenskapen är odefinierad. [Granska exempelfrågor på dokument med några saknade fält](#documents-with-missing-fields).

## <a name="examples"></a>Exempel

Här är till exempel en fråga som hämtar familjer i stigande ordning efter den inhemska stadens namn:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Resultatet är:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Följande fråga hämtar `id`familj s i ordning efter deras datum för att skapa objekt. Artikeln `creationDate` är ett tal som representerar *epoktiden*eller förfluten tid sedan den 1 januari 1970 i sekunder.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Resultatet är:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Dessutom kan du beställa efter flera egenskaper. En fråga som order efter flera egenskaper kräver ett [sammansatt index](index-policy.md#composite-indexes). Överväg följande fråga:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Den här frågan `id` hämtar familjen i stigande ordning efter ortsnamnet. Om flera objekt har samma ortnamn kommer `creationDate` frågan att ordna efter i fallande ordning.

## <a name="documents-with-missing-fields"></a>Dokument med saknade fält

Frågor med `ORDER BY` som körs mot behållare med standardindexeringsprincipen returnerar inte dokument där sorteringsegenskapen är odefinierad. Om du vill inkludera dokument där sorteringsegenskapen är odefinierad bör du uttryckligen inkludera den här egenskapen i indexeringsprincipen.

Här är till exempel en behållare med en indexeringsprincip `"/*"`som inte uttryckligen innehåller några sökvägar förutom:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Om du kör en `lastName` fråga `Order By` som ingår i satsen inkluderas endast dokument som har en `lastName` definierad egenskap. Vi har inte definierat `lastName` en explicit inkluderad sökväg för så att alla dokument utan en `lastName` inte visas i frågeresultatet.

Här är en fråga `lastName` som sorteras efter på två `lastName` dokument, varav en inte har en definierad:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Resultaten omfattar endast det dokument `lastName`som har en definierad:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Om vi uppdaterar behållarens indexeringsprincip så `lastName`att den uttryckligen innehåller en sökväg för inkluderar vi dokument med en odefinierad sorteringsegenskap i frågeresultatet. Du måste uttryckligen definiera sökvägen för att leda till det här skalärvärdet (och inte utanför det). Du bör `?` använda tecknet i sökvägsdefinitionen i indexeringsprincipen för att säkerställa att du uttryckligen indexerar egenskapen `lastName` och inga ytterligare kapslade banor utanför den. Om `Order By` frågan använder ett [sammansatt index](index-policy.md#composite-indexes)innehåller resultaten alltid dokument med en odefinierad sorteringsegenskap i frågeresultatet.

Här är ett exempel indexeringsprincip som gör att `lastName` du kan ha dokument med en odefinierad visas i frågeresultatet:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Om du kör samma fråga igen `lastName` visas dokument som saknas först i frågeresultaten:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Resultatet är:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Om du ändrar `DESC`sorteringsordningen `lastName` till visas dokument som saknas sist i frågeresultatet:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Resultatet är:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Indexeringsprinciper i Azure Cosmos DB](index-policy.md)
- [OFFSET LIMIT-sats](sql-query-offset-limit.md)
