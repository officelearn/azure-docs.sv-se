---
title: Arbeta med JSON i Azure Cosmos DB
description: Lär dig mer om att fråga och komma åt kapslade JSON-egenskaper och använda specialtecken i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: a569b0122f9122b141b64ded21dbd9be1d766a41
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699119"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Arbeta med JSON i Azure Cosmos DB

I Azure Cosmos DB SQL-API: n (Core) lagras objekten som JSON. Typ systemet och uttryck är begränsade till att bara hantera JSON-typer. Mer information finns i JSON- [specifikationen](https://www.json.org/).

Vi kommer att sammanfatta några viktiga aspekter av att arbeta med JSON:

- JSON-objekt börjar alltid med en `{` vänster klammerparentes och slutar med en `}` höger klammerparentes
- Du kan ha JSON-egenskaper [kapslade](#nested-properties) i varandra
- JSON-egenskapsvärde kan vara matriser
- JSON-egenskaps namn är Skift läges känsliga
- JSON-egenskapens namn kan vara valfritt sträng värde (inklusive blank steg eller tecken som inte är bokstäver)

## <a name="nested-properties"></a>Kapslade egenskaper

Du kan komma åt kapslad JSON med en punkt-accessor. Du kan använda kapslade JSON-egenskaper i dina frågor på samma sätt som du kan använda andra egenskaper.

Här är ett dokument med kapslad JSON:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

I det här fallet `state` `country` är egenskaperna, och `city` alla kapslade i `address` egenskapen.

I följande exempel projekterar två kapslade egenskaper: `f.address.state` och `f.address.city` .

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Arbeta med matriser

Förutom kapslade egenskaper stöder JSON också matriser.

Här är ett exempel dokument med en matris:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

När du arbetar med matriser kan du komma åt ett speciellt element i matrisen genom att referera till dess position:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

I de flesta fall använder du dock en under [fråga](sql-query-subquery.md) eller [själv koppling](sql-query-join.md) när du arbetar med matriser.

Här är till exempel ett dokument som visar den dagliga balansen för en kunds bank konto.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Om du vill köra en fråga som visade alla kunder som hade ett negativt saldo vid [något tillfälle kan du använda en](sql-query-subquery.md#exists-expression) under fråga:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="reserved-keywords-and-special-characters-in-json"></a>Reserverade nyckelord och specialtecken i JSON

Du kan komma åt egenskaper med hjälp av den citerade egenskaps operatorn `[]` . Till exempel är `SELECT c.grade` och `SELECT c["grade"]` likvärdiga. Den här syntaxen är användbar för att undvika en egenskap som innehåller blank steg, specialtecken eller har samma namn som ett SQL-nyckelord eller reserverat ord.

Här är till exempel ett dokument med en egenskap med namnet `order` och en egenskap `price($)` som innehåller specialtecken:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Om du kör en fråga som innehåller `order` egenskapen eller `price($)` egenskapen visas ett syntaxfel.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

Resultatet är:

`
Syntax error, incorrect syntax near 'order'
`

Du bör skriva om samma frågor som nedan:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>JSON-uttryck

Projektion stöder även JSON-uttryck, som du ser i följande exempel:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

I föregående exempel `SELECT` måste satsen skapa ett JSON-objekt och eftersom exemplet inte tillhandahåller någon nyckel använder-satsen det implicita argumentet variabel namn `$1` . Följande fråga returnerar två implicita argument-variabler: `$1` och `$2` .

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Alias

Du kan uttryckligen Ali Aset värden i frågor. Om en fråga har två egenskaper med samma namn, använder du alias för att byta namn på en eller båda av egenskaperna så att de är disambiguated i det planerade resultatet.

### <a name="examples"></a>Exempel

`AS`Nyckelordet som används för alias är valfritt, som visas i följande exempel när du projicerar det andra värdet som `NameInfo` :

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Alias med reserverade nyckelord eller specialtecken

Du kan inte använda alias för att projicera ett värde som ett egenskaps namn med blank steg, specialtecken eller reserverade ord. Om du vill ändra ett värdes projektion till till exempel ha ett egenskaps namn med ett blank steg kan du använda ett [JSON-uttryck](#json-expressions).

Här är ett exempel:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-sats](sql-query-select.md)
- [WHERE-sats](sql-query-where.md)
