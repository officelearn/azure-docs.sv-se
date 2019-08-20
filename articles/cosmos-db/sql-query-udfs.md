---
title: 'Användardefinierade funktioner (UDF: er) i Azure Cosmos DB'
description: Lär dig mer om användardefinierade funktioner i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614328"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Användardefinierade funktioner (UDF: er) i Azure Cosmos DB

SQL API ger stöd för användardefinierade funktioner (UDF: er). Med skalär UDF: er kan du skicka med noll eller många argument och returnera ett enskilt argument resultat. API: et kontrollerar varje argument för att vara juridiska JSON-värden.  

API: et utökar SQL-syntaxen för att stödja anpassad program logik med UDF: er. Du kan registrera UDF: er med SQL-API: et och referera dem i SQL-frågor. Faktum är att UDF:er har utmärkt utformning för att anropas från frågor. UDF: er har inte åtkomst till objektet Context, t. ex. andra JavaScript-typer, till exempel lagrade procedurer och utlösare. Frågorna är skrivskyddade och kan köras antingen på primära eller sekundära repliker. UDF: er, till skillnad från andra JavaScript-typer, är utformade för att köras på sekundära repliker.

I följande exempel registreras en UDF under en objekt behållare i Cosmos-databasen. I exemplet skapas en UDF vars namn är `REGEX_MATCH`. Det accepterar två JSON- `input` sträng värden och `pattern`kontrollerar om det första matchar mönstret som anges i `string.match()` den andra med hjälp av JavaScript-funktionen.

## <a name="examples"></a>Exempel

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Använd nu denna UDF i en fråga-projektion. Du måste kvalificera UDF: er med Skift läges känsligt `udf.` prefix när du anropar dem inifrån frågor.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Resultaten är:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Du kan använda UDF-filen som är `udf.` kvalificerad med prefixet inuti ett filter, som i följande exempel:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Resultaten är:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

I själva verket är UDF: er giltiga skalära uttryck som du kan använda i både projektioner och filter.

Om du vill utöka kraften i UDF: er kan du titta på ett annat exempel med villkorlig logik:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

I följande exempel övningaras UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Resultaten är:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Om egenskaperna som anges av UDF-parametrarna inte är tillgängliga i JSON-värdet betraktas parametern som odefinierad och UDF-anropet hoppas över. Om resultatet av UDF-filen är odefinierat inkluderas det inte i resultatet.

Som föregående exempel visar UDF: er integrerar du kraften hos JavaScript-språket med SQL-API: et. UDF: er tillhandahåller ett omfattande programmerbart gränssnitt för att göra komplexa procedurer, villkorlig logik med hjälp av inbyggda funktioner för JavaScript-körning. SQL-API: et tillhandahåller argumenten för UDF: er för varje käll objekt med det aktuella WHERE-eller SELECT-sats stadiet bearbetning. Resultatet är sömlöst införlivat i den övergripande körnings pipelinen. I sammanfattning är UDF: er fantastiska verktyg för att utföra komplex affärs logik som en del av frågorna.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [System funktioner](sql-query-system-functions.md)
- [Agg regeringar](sql-query-aggregates.md)