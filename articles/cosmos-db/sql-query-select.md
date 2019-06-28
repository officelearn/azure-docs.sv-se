---
title: SELECT-satsen i Azure Cosmos DB
description: Läs mer om SQL SELECT-satsen för Azure Cosmos DB. Använd SQL som ett Azure Cosmos DB JSON-frågespråket.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 84d0212f7f212b4554b506726e027fe51f795eea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342626"
---
# <a name="select-clause"></a>SELECT-satsen

Varje fråga består av en SELECT-sats och en valfri [FROM](sql-query-from.md) och [där](sql-query-where.md) satser per ANSI SQL-standarder. Normalt källan i FROM-satsen räknas och WHERE-satsen används ett filter på källan för att hämta en delmängd av JSON-objekt. SELECT-satsen genererar sedan de begärda JSON-värden i select-listan.

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

  Egenskaper för eller -värde som ska väljas för resultatuppsättningen.  
  
- `'*'`  

  Anger att värdet ska hämtas utan några ändringar. Mer specifikt om bearbetade värdet är ett objekt, hämtas alla egenskaper.  
  
- `<object_property_list>`  
  
  Anger listan över egenskaper som ska hämtas. Varje returnerade värdet ska vara ett objekt med egenskaper som anges.  
  
- `VALUE`  

  Anger att JSON-värde ska hämtas i stället för det fullständiga JSON-objektet. Detta, till skillnad från `<property_list>` radbryts inte det beräknade värdet i ett objekt.  
 
- `DISTINCT`
  
  Anger att dubbletter av beräknade egenskaper bör tas bort.  

- `<scalar_expression>`  

  Uttryck som representerar värdet som ska beräknas. Se [skaläruttryck](sql-query-scalar-expressions.md) information.  

## <a name="remarks"></a>Kommentarer

Den `SELECT *` syntax är bara giltigt om FROM-satsen har deklarerats exakt ett alias. `SELECT *` innehåller en identity-projektion, som kan vara användbart om inga projektion krävs. Välj * är bara giltigt om FROM-satsen har angetts och införs bara en enda Indatakällan.  
  
Båda `SELECT <select_list>` och `SELECT *` är ”syntaktiska socker” och du kan också kan uttryckas med hjälp av enkla SELECT-instruktioner som visas nedan.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   motsvarar att:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   motsvarar att:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exempel

Välj följande fråga exempel returnerar `address` från `Families` vars `id` matchar `AndersenFamily`:

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

### <a name="quoted-property-accessor"></a>Citerade Egenskapsåtkomst
Du kan komma åt egenskaper med hjälp av citerade egenskapen operatorn []. Till exempel är `SELECT c.grade` och `SELECT c["grade"]` likvärdiga. Den här syntaxen är användbart för att undvika en egenskap som innehåller blanksteg, specialtecken, eller har samma namn som en SQL-nyckelord eller reserverat ord.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Kapslade egenskaper

I följande exempel projekt två kapslade egenskaper `f.address.state` och `f.address.city`.

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

SELECT-satsen i föregående exempel, som behövs för att skapa en JSON-objekt och eftersom det innehåller ingen nyckel, i satsen använder implicit argumentet variabelnamnet `$1`. Följande fråga returnerar två argumentvariabler som implicit: `$1` och `$2`.

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

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [WHERE-satsen](sql-query-where.md)