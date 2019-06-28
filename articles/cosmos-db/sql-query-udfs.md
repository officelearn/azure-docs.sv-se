---
title: Användardefinierade funktioner (UDF) i Azure Cosmos DB
description: Läs mer om användardefinierade funktioner i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342600"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Användardefinierade funktioner (UDF) i Azure Cosmos DB

SQL-API har stöd för användardefinierade funktioner (UDF). Du kan skicka in noll eller flera argument och returnera ett enda argument-resultat med skalära UDF: er. API: et kontrollerar varje argument för att du är juridiska JSON-värden.  

API: et utökar den SQL-syntaxen för att stöder anpassad programlogik med UDF: er. Du kan registrera UDF: er med SQL-API: T och referera till dem i SQL-frågor. Faktum är att UDF:er har utmärkt utformning för att anropas från frågor. Följd har UDF: er inte åtkomst till context-objektet som andra JavaScript-typer, till exempel lagrade procedurer och utlösare. Frågor är skrivskyddad och kan köras antingen på primära eller sekundära repliker. Användardefinierade funktioner, till skillnad från andra JavaScript-typer är avsedda att köras på sekundära repliker.

I följande exempel registreras en UDF under en objektbehållaren i Cosmos DB-databasen. I exemplet skapas en UDF vars namn är `REGEX_MATCH`. Godtas två JSON-strängvärden `input` och `pattern`, och kontrollerar om de första matchar mönstret som anges i andra med hjälp av JavaScript- `string.match()` funktion.

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

Nu kan använda den här UDF i en fråga projektion. Du måste är berättigad UDF: er med skiftlägeskänsliga prefixet `udf.` när du anropar dem från inom frågor.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Resultatet är:

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

Du kan använda UDF kvalificerad med den `udf.` prefix i ett filter, som i följande exempel:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Resultatet är:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

I princip är UDF: er giltig skalärt uttryck som du kan använda i både projektioner och filter.

Titta på ett annat exempel med villkorsstyrd logik för att expandera på kraften i UDF: er:

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

I följande exempel utövar UDF-filen:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Resultatet är:

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

Om egenskaperna som anges av en användardefinierad funktion parametrar inte är tillgängliga i JSON-värde, parametern betraktas som en odefinierad och hoppas över UDF-anrop. På samma sätt, om resultatet av en användardefinierad funktion är odefinierad den ingår inte i resultatet.

Föregående exempel visas integrera UDF: er kraften i JavaScript-språket med SQL API. UDF: er ger ett omfattande programmerbart gränssnitt för att göra komplex procedurmässig, villkorsstyrd logik med hjälp av inbyggda funktioner för JavaScript-körning. SQL-API: et tillhandahåller argumenten till de UDF: er för varje källa-objekt på den aktuella var eller SELECT-satsen steg i processen. Resultatet är sömlöst integrerat i övergripande körning pipelinen. Sammanfattningsvis är UDF: er fantastiska verktyg för att göra komplicerad affärslogik som en del av frågor.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Systemfunktioner](sql-query-system-functions.md)
- [Aggregeringar](sql-query-aggregates.md)