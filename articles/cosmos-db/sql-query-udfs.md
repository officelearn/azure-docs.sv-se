---
title: Användardefinierade funktioner (UDFs) i Azure Cosmos DB
description: Lär dig mer om användardefinierade funktioner i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011131"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Användardefinierade funktioner (UDFs) i Azure Cosmos DB

SQL API ger stöd för användardefinierade funktioner (UDFs). Med skalbara UDF:er kan du skicka in noll eller många argument och returnera ett enda argumentresultat. API:et kontrollerar varje argument för att vara lagliga JSON-värden.  

## <a name="udf-use-cases"></a>UDF användningsfall

API:et utökar SQL-syntaxen för att stödja anpassad programlogik med udfs. Du kan registrera UDFs med SQL API och referera till dem i SQL-frågor. Till skillnad från lagrade procedurer och utlösare är UDFs skrivskyddade.

Med UDF:er kan du utöka Azure Cosmos DB:s frågespråk. UDF:er är ett bra sätt att uttrycka komplex affärslogik i en frågas projektion.

Vi rekommenderar dock att du undviker UDF när:

- Det finns redan en motsvarande [systemfunktion](sql-query-system-functions.md) i Azure Cosmos DB. Systemfunktioner kommer alltid att använda färre RU: s än motsvarande UDF.
- UDF är det enda `WHERE` filtret i satsen i frågan. UDF: s inte använder indexet så att utvärdera UDF kommer att kräva lastning dokument. Genom att kombinera ytterligare filter predikater som använder indexet, i `WHERE` kombination med en UDF, i satsen minskar antalet dokument som bearbetas av UDF.

Om du måste använda samma UDF flera gånger i en fråga bör du referera till UDF i en [underfråga,](sql-query-subquery.md#evaluate-once-and-reference-many-times)så att du kan använda ett JOIN-uttryck för att utvärdera UDF en gång men referera till den många gånger.

## <a name="examples"></a>Exempel

I följande exempel registreras en UDF under en artikelbehållare i Cosmos-databasen. Exemplet skapar en UDF vars namn är `REGEX_MATCH`. Den accepterar två JSON-strängvärden `input` och `pattern`, och kontrollerar om det första matchar `string.match()` det mönster som anges i det andra med JavaScript-funktionen.

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