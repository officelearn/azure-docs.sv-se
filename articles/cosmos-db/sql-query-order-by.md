---
title: ORDER BY-sats i Azure Cosmos DB
description: Läs mer om SQL ORDER BY-satsen för Azure Cosmos DB. Använd SQL som Azure Cosmos DB JSON-frågespråk.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 5cae2bdd7d1f2f26e626c81ea95d2cee3cc8ae13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444798"
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
  
   Se den [skaläruttryck](sql-query-scalar-expressions.md) information.  
  
- `ASC | DESC`  
  
   Anger att värdena i den angivna kolumnen ska sorteras i stigande eller fallande ordning. ASC sorterar från det lägsta värdet för högsta värden. DESC sorterar från högsta värdet till lägsta värdet. ASC är standardsorteringsordning. Null-värden behandlas som de lägsta möjliga värdena.  
  
## <a name="remarks"></a>Anmärkningar  
  
   ORDER BY-satsen kräver att indexerings principen inkluderar ett index för fälten som sorteras. Azure Cosmos DB Query runtime stöder sortering mot ett egenskaps namn och inte mot beräknade egenskaper. Azure Cosmos DB stöder flera ORDER BY-egenskaper. För att kunna köra en fråga med flera ORDER BY-egenskaper bör du definiera ett [sammansatt index](index-policy.md#composite-indexes) för fälten som sorteras.
   
> [!Note] 
> När du använder .NET SDK-3.4.0 eller senare, och om egenskaperna som sorteras mot kan vara odefinierade för vissa dokument, måste du uttryckligen skapa ett index för dessa egenskaper. Standard indexerings principen tillåter inte hämtning av dokument där sorterings egenskapen är odefinierad.

## <a name="examples"></a>Exempel

Här är ett exempel på en fråga som hämtar familjer i stigande ordning efter den inhemska ortens namn:

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

Följande fråga hämtar familj `id`s i ordning efter deras objekts skapande datum. Objekt `creationDate` är ett tal som representerar den *Epoka tiden*, eller förfluten tid sedan Jan. 1, 1970 på några sekunder.

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

Dessutom kan du sortera efter flera egenskaper. En fråga som sorteras efter flera egenskaper kräver ett [sammansatt index](index-policy.md#composite-indexes). Överväg följande fråga:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Den här frågan hämtar familje `id` i stigande ordning efter Orts namnet. Om flera objekt har samma Orts namn, sorteras frågan efter `creationDate` i fallande ordning.

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-satsen](sql-query-select.md)
- [OFFSET LIMIT-sats](sql-query-offset-limit.md)
