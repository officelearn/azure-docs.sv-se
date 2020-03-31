---
title: SELECT-satsen i Azure Cosmos DB
description: Läs mer om SQL SELECT-satsen för Azure Cosmos DB. Använd SQL som ett Azure Cosmos DB JSON-frågespråk.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469943"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT-satsen i Azure Cosmos DB

Varje fråga består av en SELECT-sats och [valfria FROM-](sql-query-from.md) och [WHERE-satser,](sql-query-where.md) enligt ANSI SQL-standarder. Vanligtvis räknas källan i FROM-satsen upp och WHERE-satsen använder ett filter på källan för att hämta en delmängd av JSON-objekt. SELECT-satsen projicerar sedan de begärda JSON-värdena i urvalslistan.

## <a name="syntax"></a>Syntax

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Argument
  
- `<select_specification>`  

  Egenskaper eller värde som ska väljas för resultatuppsättningen.  
  
- `'*'`  

  Anger att värdet ska hämtas utan att göra några ändringar. Om det bearbetade värdet är ett objekt hämtas alla egenskaper.  
  
- `<object_property_list>`  
  
  Anger listan över egenskaper som ska hämtas. Varje returnerat värde kommer att vara ett objekt med de angivna egenskaperna.  
  
- `VALUE`  

  Anger att JSON-värdet ska hämtas i stället för det fullständiga JSON-objektet. Detta, `<property_list>` till skillnad från, inte radbryt det projicerade värdet i ett objekt.  
 
- `DISTINCT`
  
  Anger att dubbletter av projicerade egenskaper ska tas bort.  

- `<scalar_expression>`  

  Uttryck som representerar det värde som ska beräknas. Mer information finns i avsnittet [Skalningsuttryck.](sql-query-scalar-expressions.md)  

## <a name="remarks"></a>Anmärkningar

Syntaxen `SELECT *` är endast giltig om FROM-satsen har deklarerat exakt ett alias. `SELECT *`tillhandahåller en identitetsprojektion, vilket kan vara användbart om ingen projektion behövs. SELECT * är endast giltigt om FROM-satsen anges och endast introduceras en enda indatakälla.  
  
Båda `SELECT <select_list>` `SELECT *` och är "syntaktiskt socker" och kan alternativt uttryckas med hjälp av enkla SELECT-satser som visas nedan.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   motsvarar:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   motsvarar:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exempel

Följande SELECT-frågeexempel `id` `AndersenFamily`returnerar `address` vars `Families` matchningar:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Accessor med citatomsluten egenskap
Du kan komma åt egenskaper med den citerade egenskapsoperatören []. Till exempel är `SELECT c.grade` och `SELECT c["grade"]` likvärdiga. Den här syntaxen är användbar för att komma bort från en egenskap som innehåller blanksteg, specialtecken eller har samma namn som ett SQL-nyckelord eller reserverat ord.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Kapslade egenskaper

I följande exempel projiceras `f.address.state` två `f.address.city`kapslade egenskaper och .

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
### <a name="json-expressions"></a>JSON-uttryck

Projektion stöder också JSON-uttryck, som visas i följande exempel:

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

I föregående exempel måste SELECT-satsen skapa ett JSON-objekt, och eftersom exemplet inte innehåller `$1`någon nyckel används det implicita argumentvariabelnamnet . Följande fråga returnerar två implicita argumentvariabler: `$1` och `$2`.

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
## <a name="reserved-keywords-and-special-characters"></a>Reserverade nyckelord och specialtecken

Om dina data innehåller egenskaper med samma namn som reserverade nyckelord som "ordning" eller "Grupp" resulterar frågorna mot dessa dokument i syntaxfel. Du bör uttryckligen inkludera `[]` egenskapen som tecken för att köra frågan.

Här är till exempel ett dokument `order` med en `price($)` namngiven egenskap och en egenskap som innehåller specialtecken:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Om du kör en fråga `order` som `price($)` innehåller egenskapen eller egenskapen visas ett syntaxfel.

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

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE-klausul](sql-query-where.md)
