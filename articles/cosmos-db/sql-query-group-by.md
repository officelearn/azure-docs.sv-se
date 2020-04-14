---
title: GROUP BY-satsen i Azure Cosmos DB
description: Läs mer om GROUP BY-satsen för Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 8a3cbbafc066747b62f79934f2cd12301aa1ba17
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261609"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GROUP BY-satsen i Azure Cosmos DB

SATSGRUPP BY delar upp frågans resultat enligt värdena för en eller flera angivna egenskaper.

> [!NOTE]
> Azure Cosmos DB stöder för närvarande GROUP BY i .NET SDK 3.3 och högre samt JavaScript SDK 3.4 och högre.
> Stöd för andra språk SDK är för närvarande inte tillgängligt men planeras.

## <a name="syntax"></a>Syntax

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argument

- `<scalar_expression_list>`

   Anger de uttryck som ska användas för att dela upp frågeresultat.

- `<scalar_expression>`
  
   Alla skaläruttryck tillåts förutom scalar subqueries och skaläraggregat. Varje skaläruttryck måste innehålla minst en egenskapsreferens. Det finns ingen gräns för antalet enskilda uttryck eller kardinaliteten för varje uttryck.

## <a name="remarks"></a>Anmärkningar
  
  När en fråga använder en GROUP BY-sats kan SELECT-satsen bara innehålla delmängden av egenskaper och systemfunktioner som ingår i SATS FÖR GRUPP. Ett undantag är [aggregerade systemfunktioner](sql-query-aggregates.md), som kan visas i SELECT-satsen utan att ingå i GROUP BY-satsen. Du kan också alltid inkludera litterala värden i SELECT-satsen.

  Sats för grupp måste vara efter satsen SELECT, FROM och WHERE och före OFFSET LIMIT-satsen. Du kan för närvarande inte använda GROUP BY med en ORDER BY-sats, men detta är planerat.

  GROUP BY-klausulen tillåter inte något av följande:
  
- Aliasegenskaper eller aliassystemfunktioner (aliasing är fortfarande tillåtet inom SELECT-satsen)
- Underfrågor
- Aggregerade systemfunktioner (dessa är endast tillåtna i SELECT-satsen)

Frågor med en mängdsystemfunktion och en `GROUP BY` underkvent med stöds inte. Följande fråga stöds till exempel inte:

```sql
SELECT COUNT(UniqueLastNames) FROM (SELECT AVG(f.age) FROM f GROUP BY f.lastName) AS UniqueLastNames
```

## <a name="examples"></a>Exempel

I de här exemplen används den näringsdatauppsättning som är tillgänglig via [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

Här är till exempel en fråga som returnerar det totala antalet artiklar i varje foodGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Vissa resultat är (TOP sökord används för att begränsa resultaten):

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Den här frågan har två uttryck som används för att dela upp resultat:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Några resultat är:

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Den här frågan har en systemfunktion i SATS FÖR GROUP:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Några resultat är:

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

Den här frågan använder både nyckelord och systemfunktioner i objektegenskapsuttrycket:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Resultatet är:

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-sats](sql-query-select.md)
- [Mängdfunktioner](sql-query-aggregates.md)
