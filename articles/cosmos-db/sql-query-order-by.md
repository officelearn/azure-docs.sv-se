---
title: ORDER BY-sats i Azure Cosmos DB
description: Läs mer om SQL ORDER BY-satsen för Azure Cosmos DB. Använd SQL som Azure Cosmos DB JSON-frågespråk.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188735"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY-sats i Azure Cosmos DB

Den valfria ORDER BY-satsen anger sorterings ordningen för resultat som returneras av frågan.

## <a name="syntax"></a>Syntax
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argument
  
- `<sort_specification>`  
  
   Anger en egenskap eller ett uttryck som du vill sortera frågeresultatet. En sorterings kolumn kan anges som ett namn eller ett egenskaps-alias.  
  
   Flera egenskaper kan anges. Egenskaps namn måste vara unika. Ordningen på sorterings egenskaperna i ORDER BY-satsen definierar organisationen för den sorterade resultat uppsättningen. Det vill säga resultatet sorteras efter den första egenskapen och sedan den beställda listan sorteras efter den andra egenskapen och så vidare.  
  
   De egenskaps namn som refereras i ORDER BY-satsen måste motsvara antingen en egenskap i SELECT-listan eller till en egenskap som har definierats i mängden som anges i from-satsen utan tvetydigheter.  
  
- `<sort_expression>`  
  
   Anger en eller flera egenskaper eller uttryck som du kan använda för att sortera frågeresultatet.  
  
- `<scalar_expression>`  
  
   Mer information finns i avsnittet [skalära uttryck](sql-query-scalar-expressions.md) .  
  
- `ASC | DESC`  
  
   Anger att värdena i den angivna kolumnen ska sorteras i stigande eller fallande ordning. ASC sorterar från det lägsta värdet för högsta värden. DESC sorterar från högsta värdet till lägsta värdet. ASC är standardsorteringsordning. Null-värden behandlas som de lägsta möjliga värdena.  
  
## <a name="remarks"></a>Kommentarer  
  
   `ORDER BY`-satsen kräver att indexerings principen inkluderar ett index för fälten som sorteras. Azure Cosmos DB Query runtime stöder sortering mot ett egenskaps namn och inte mot beräknade egenskaper. Azure Cosmos DB stöder flera `ORDER BY`s egenskaper. För att kunna köra en fråga med flera ORDER BY-egenskaper bör du definiera ett [sammansatt index](index-policy.md#composite-indexes) för fälten som sorteras.

> [!Note]
> Om egenskaperna som sorteras kan vara odefinierade för vissa dokument och du vill hämta dem i en ORDER BY-fråga, måste du uttryckligen ta med den här sökvägen i indexet. Standard indexerings principen tillåter inte hämtning av dokument där sorterings egenskapen är odefinierad. [Granska exempel frågor i dokument med några fält som saknas](#documents-with-missing-fields).

## <a name="examples"></a>Exempel

Här är ett exempel på en fråga som hämtar familjer i stigande ordning efter den inhemska ortens namn:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Resultaten är:

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

Följande fråga hämtar familj `id`s i ordning efter deras objekts skapande datum. Objekt `creationDate` är ett tal som representerar den *Epoka tiden*, eller förfluten tid sedan Jan. 1, 1970 på några sekunder.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Resultaten är:

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

Dessutom kan du sortera efter flera egenskaper. En fråga som sorteras efter flera egenskaper kräver ett [sammansatt index](index-policy.md#composite-indexes). Tänk på följande fråga:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Den här frågan hämtar familje `id` i stigande ordning efter Orts namnet. Om flera objekt har samma Orts namn, sorteras frågan efter `creationDate` i fallande ordning.

## <a name="documents-with-missing-fields"></a>Dokument med fält som saknas

Frågor med `ORDER BY` som körs mot behållare med standard indexerings principen returnerar inte dokument där sorterings egenskapen är odefinierad. Om du vill inkludera dokument där sorterings egenskapen är odefinierad bör du uttryckligen ta med den här egenskapen i indexerings principen.

Här är ett exempel på en behållare med en indexerings princip som inte uttryckligen innehåller några sökvägar förutom `"/*"`:

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

Om du kör en fråga som innehåller `lastName` i `Order By`-satsen, kommer resultatet endast att inkludera dokument som har en definierad `lastName` egenskap. Vi har inte definierat någon uttrycklig sökväg för `lastName` så att alla dokument utan `lastName` inte visas i frågeresultatet.

Här är en fråga som sorteras efter `lastName` på två dokument, som inte har något definierat `lastName`:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Resultaten innehåller bara det dokument som har en definierad `lastName`:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Om vi uppdaterar behållarens indexerings princip för att explicit inkludera en sökväg för `lastName`, kommer vi att inkludera dokument med en odefinierad sorterings egenskap i frågeresultatet. Du måste uttryckligen definiera sökvägen för att leda till det här skalära värdet (och inte bortom det). Du bör använda `?` Character i Sök vägs definitionen i indexerings principen för att se till att du explicit indexerar egenskapen `lastName` och inga ytterligare inkapslade sökvägar.

Här är ett exempel på en indexerings princip som gör att du kan låta dokument med ett odefinierat `lastName` visas i frågeresultatet:

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

Om du kör samma fråga igen visas dokument som saknas `lastName` först i frågeresultatet:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Resultaten är:

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

Om du ändrar sorterings ordningen till `DESC`visas dokument som saknas `lastName` sist i frågeresultatet:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Resultaten är:

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
