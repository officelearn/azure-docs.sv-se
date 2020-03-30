---
title: Användardefinierade funktioner (UDFs) i Azure Cosmos DB
description: Lär dig mer om användardefinierade funktioner i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614328"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Användardefinierade funktioner (UDFs) i Azure Cosmos DB

SQL API ger stöd för användardefinierade funktioner (UDFs). Med skalbara UDF:er kan du skicka in noll eller många argument och returnera ett enda argumentresultat. API:et kontrollerar varje argument för att vara lagliga JSON-värden.  

API:et utökar SQL-syntaxen för att stödja anpassad programlogik med udfs. Du kan registrera UDFs med SQL API och referera till dem i SQL-frågor. Faktum är att UDF:er har utmärkt utformning för att anropas från frågor. Som en naturlig följd har UDF:er inte åtkomst till kontextobjektet som andra JavaScript-typer, till exempel lagrade procedurer och utlösare. Frågor är skrivskyddade och kan köras antingen på primära eller sekundära repliker. UDF:er, till skillnad från andra JavaScript-typer, är utformade för att köras på sekundära repliker.

I följande exempel registreras en UDF under en artikelbehållare i Cosmos-databasen. Exemplet skapar en UDF vars namn är `REGEX_MATCH`. Den accepterar två JSON-strängvärden `input` och `pattern`, och kontrollerar om det första matchar `string.match()` det mönster som anges i det andra med JavaScript-funktionen.

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

Använd nu den här UDF:en i en frågeprojektion. Du måste kvalificera UDF:er `udf.` med det skiftlägeskänsliga prefixet när du anropar dem inifrån frågor.

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

Du kan använda UDF-enheten som är kvalificerad med prefixet `udf.` i ett filter, som i följande exempel:

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

I huvudsak är UDF:er giltiga skaluttryck som du kan använda i både projektioner och filter.

Om du vill utöka kraften i UDF:er kan du titta på ett annat exempel med villkorsstyrd logik:

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

I följande exempel övnings UDF:

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

Om egenskaperna som refereras till av UDF-parametrarna inte är tillgängliga i JSON-värdet betraktas parametern som odefinierad och UDF-anropet hoppas över. På samma sätt, om resultatet av UDF är odefinierat, ingår det inte i resultatet.

Som de föregående exemplen visar integrerar UDFs kraften i JavaScript-språket med SQL API. UFS ger ett omfattande programmerbart gränssnitt för att göra komplexa procedurmässiga, villkorlig logik med hjälp av inbyggda JavaScript-körningsfunktioner. SQL API innehåller argumenten till UDF:erna för varje källobjekt i det aktuella VAR- eller SELECT-satsen i bearbetningen. Resultatet är sömlöst införlivat i den övergripande körningspipelinen. Sammanfattningsvis är UDF:er bra verktyg för att göra komplex affärslogik som en del av frågor.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Systemfunktioner](sql-query-system-functions.md)
- [Samlingar](sql-query-aggregates.md)