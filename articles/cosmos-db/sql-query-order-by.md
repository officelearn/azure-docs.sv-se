---
title: ORDER BY-sats i Azure Cosmos DB
description: Läs mer om SQL ORDER BY-sats för Azure Cosmos DB. Använd SQL som ett Azure Cosmos DB JSON-frågespråket.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342791"
---
# <a name="order-by-clause"></a>ORDER BY-sats

Valfritt ORDER BY-satsen anger sorteringsordning för resultaten som returnerades av frågan.

## <a name="syntax"></a>Syntax
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argument
  
- `<sort_specification>`  
  
   Anger en egenskap eller ett uttryck som du vill sortera frågeresultatet. En sorteringskolumn kan anges som ett namn eller egenskapen alias.  
  
   Flera egenskaper kan anges. Egenskapsnamn måste vara unikt. Sekvens med egenskaperna sortera i ORDER BY-satsen definierar hur sorterade resultatuppsättningen. Det vill säga resultatet sorteras efter den första egenskapen och sedan den beställda listan sorteras efter den andra egenskapen och så vidare.  
  
   Egenskapsnamn som refereras i ORDER BY-satsen måste motsvara till antingen en egenskap i select-listan eller till en egenskap som definierats i den samling som har angetts i FROM-sats utan någon tvetydigheter.  
  
- `<sort_expression>`  
  
   Anger en eller flera egenskaper eller uttryck som du vill sortera frågeresultatet.  
  
- `<scalar_expression>`  
  
   Se den [skaläruttryck](sql-query-scalar-expressions.md) information.  
  
- `ASC | DESC`  
  
   Anger att värdena i den angivna kolumnen ska sorteras i stigande eller fallande ordning. ASC sorterar från det lägsta värdet för högsta värden. DESC sorterar från högsta värdet till lägsta värdet. ASC är standardsorteringsordning. Null-värden behandlas som de lägsta möjliga värdena.  
  
## <a name="remarks"></a>Kommentarer  
  
   ORDER BY-satsen kräver att indexprincip inkluderar ett index för att sortera. Azure Cosmos DB-fråga runtime stöder sortering mot ett egenskapsnamn och inte mot beräknade egenskaper. Azure Cosmos DB stöder flera ORDER BY-egenskaper. För att köra en fråga med flera ORDER BY-egenskaper, bör du definiera en [sammansatta index](index-policy.md#composite-indexes) på att sortera.

## <a name="examples"></a>Exempel

Här är till exempel en fråga som hämtar familjer i stigande ordning efter den fasta Ortnamn:

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

Följande fråga hämtar familj `id`s efter skapandedatum sina objekt. Objektet `creationDate` är ett tal som representerar den *epoktid*, eller förfluten tid sedan den 1 januari 1970 på några sekunder.

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

Dessutom kan du sortera efter flera egenskaper. En fråga som grupperas efter flera egenskaper kräver en [sammansatta index](index-policy.md#composite-indexes). Överväg följande fråga:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Den här frågan hämtar familjen `id` i stigande ordning efter namnet på staden. Om flera objekt har samma stad namn kan frågan ska sortera efter den `creationDate` i fallande ordning.

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-satsen](sql-query-select.md)
- [GRÄNSEN för OFFSET-sats](sql-query-offset-limit.md)
