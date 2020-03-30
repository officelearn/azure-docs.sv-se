---
title: SQL-underfrågor för Azure Cosmos DB
description: Lär dig mer om SQL-underfrågor och deras vanliga användningsfall och olika typer av underkategorier i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870572"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>EXEMPEL på SQL-underkrymning för Azure Cosmos DB

En underfråga är en fråga som är kapslad i en annan fråga. En underfråga kallas också en inre fråga eller en inre markering. Uttrycket som innehåller en underfråga kallas vanligtvis en yttre fråga.

I den här artikeln beskrivs SQL-underfrågor och deras vanliga användningsfall i Azure Cosmos DB. Alla exempelfrågor i det här dokumentet kan köras mot en näringsdatauppsättning som är förinläst på [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Typer av underkategorier

Det finns två huvudtyper av underfrågor:

* **Korrelerad**: En underfråga som refererar till värden från den yttre frågan. Underfrågan utvärderas en gång för varje rad som den yttre frågan bearbetar.
* **Icke-korrelerad:** En underfråga som är oberoende av den yttre frågan. Den kan köras på egen hand utan att förlita sig på den yttre frågan.

> [!NOTE]
> Azure Cosmos DB stöder endast korrelerade underfrågor.

Underfrågor kan klassificeras ytterligare baserat på antalet rader och kolumner som de returnerar. Det finns tre typer:
* **Tabell**: Returnerar flera rader och flera kolumner.
* **Flera värden**: Returnerar flera rader och en enskild kolumn.
* **Skalär**: Returnerar en enda rad och en enskild kolumn.

SQL-frågor i Azure Cosmos DB returnerar alltid en enda kolumn (antingen ett enkelt värde eller ett komplext dokument). Därför är endast subkvaltar med flera värde och skalär tillämpliga i Azure Cosmos DB. Du kan bara använda en subquery med flera värden i FROM-satsen som ett relationsuttryck. Du kan använda en scalar-underkräktning som ett skaläruttryck i SELECT- eller WHERE-satsen, eller som ett relationsuttryck i FROM-satsen.

## <a name="multi-value-subqueries"></a>Underfrågor med flera värden

Underfrågor med flera värden returnerar en uppsättning dokument och används alltid inom FROM-satsen. De används för:

* Optimera JOIN-uttryck. 
* Utvärdera dyra uttryck en gång och referera flera gånger.

## <a name="optimize-join-expressions"></a>Optimera JOIN-uttryck

Underfrågor med flera värden kan optimera JOIN-uttryck genom att trycka predikat efter varje select-many-uttryck i stället för korskopplingar i WHERE-satsen.

Överväg följande fråga:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

För den här frågan matchar indexet alla dokument som har en tagg med namnet "modersmjölksersättning". Det är ett näringsobjekt med ett värde mellan 0 och 10 och ett serveringsobjekt med en mängd som är större än 1. JOIN-uttrycket här kommer att utföra korsprodukten för alla objekt av taggar, näringsämnen och serveringar matriser för varje matchande dokument innan något filter tillämpas. 

WHERE-satsen kommer sedan att tillämpa filter predikat på varje <c, t, n, s> tuppel. Om ett matchande dokument till exempel hade 10 objekt i var och en av de tre matriserna, expanderas det till 1 x 10 x 10 x 10 (det vill än 1 000) tupplar. Om du använder underfrågor här kan du filtrera bort kopplade matrisobjekt innan du går med i nästa uttryck.

Den här frågan motsvarar den föregående men använder underfrågor:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Anta att endast ett objekt i taggmatrisen matchar filtret och att det finns fem objekt för både näringsämnen och portioner. JOIN-uttrycken utökas sedan till 1 x 1 x 5 x 5 = 25 objekt, jämfört med 1 000 objekt i den första frågan.

## <a name="evaluate-once-and-reference-many-times"></a>Utvärdera en gång och referera till många gånger

Underfrågor kan hjälpa till att optimera frågor med dyra uttryck som användardefinierade funktioner (UDF), komplexa strängar eller aritmetiska uttryck. Du kan använda en underkö tillsammans med ett JOIN-uttryck för att utvärdera uttrycket en gång men referera till det många gånger.

Följande fråga kör UDF `GetMaxNutritionValue` två gånger:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Här är en motsvarande fråga som kör UDF bara en gång:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tänk på korsproduktbeteendet för JOIN-uttryck. Om UDF-uttrycket kan utvärderas till odefinierat bör du se till att JOIN-uttrycket alltid skapar en enda rad genom att returnera ett objekt från underfrågan i stället för värdet direkt.
>

Här är ett liknande exempel som returnerar ett objekt i stället för ett värde:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Tillvägagångssättet är inte begränsat till UDF. Det gäller alla potentiellt dyra uttryck. Du kan till exempel ha samma metod `avg`med den matematiska funktionen:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Härma koppling med externa referensdata

Du kan ofta behöva referera till statiska data som sällan ändras, till exempel måttenheter eller landskoder. Det är bättre att inte duplicera sådana data för varje dokument. Om du undviker den här dupliceringen sparas på lagring och läsprestanda förbättras genom att dokumentstorleken är mindre. Du kan använda en underklyssning för att efterlikna semantik med inre koppling med en samling referensdata.

Tänk till exempel på den här uppsättningen referensdata:

| **Enhet** | **Namn**            | **Multiplikator** | **Basenhet** |
| -------- | ------------------- | -------------- | ------------- |
| Ng       | Nanogram            | 1.00E-09       | Gram (gram)          |
| Μg       | Mikrogram           | 1.00E-06       | Gram (gram)          |
| Mg       | Milligram           | 1.00E-03       | Gram (gram)          |
| g        | Gram (gram)                | 1.00E+00       | Gram (gram)          |
| Kg       | Kilogram            | 1.00E+03       | Gram (gram)          |
| Mg       | Megagram (på)            | 1.00E+06       | Gram (gram)          |
| Gg       | Gigagram (på)            | 1.00E+09       | Gram (gram)          |
| Nj       | Nanojoule (nanojoule)           | 1.00E-09       | Joule (se)         |
| μJ (μJ)       | Mikrojoule          | 1.00E-06       | Joule (se)         |
| Mj       | Millijoule (millijoule)          | 1.00E-03       | Joule (se)         |
| J        | Joule (se)               | 1.00E+00       | Joule (se)         |
| Kj       | Kilojoule           | 1.00E+03       | Joule (se)         |
| Mj       | Megajoule (megajoule)           | 1.00E+06       | Joule (se)         |
| Gj       | Gigajoule           | 1.00E+09       | Joule (se)         |
| Cal      | Kalori             | 1.00E+00       | Kalori       |
| Kcal     | Kalori             | 1.00E+03       | Kalori       |
| Ie       | Internationella enheter |                |               |


Följande fråga härmar sammanfogningen med dessa data så att du lägger till enhetens namn i utdata:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Scalar underklöt

Ett scalar-underkrytningsuttryck är en underkvent som utvärderas till ett enda värde. Värdet för scalar-delklyet är värdet för underkventets projektion (SELECT-satsen).  Du kan använda ett scalar-underkryttningsuttryck på många ställen där ett skaläruttryck är giltigt. Du kan till exempel använda en scalar-underkräftor i alla uttryck i både SELECT- och WHERE-satserna.

Att använda en scalar subquery hjälper inte alltid optimera, dock. Om du till exempel skickar en scalar-underkläde som ett argument till antingen ett system eller användardefinierade funktioner ger det ingen fördel i förbrukning av resursenhet (RU) eller svarstid.

Skalär underkräppningar kan vidare klassificeras som:
* Underklöt underklöt med enkelt uttryck
* Aggregerade skalärunderkvätningar

## <a name="simple-expression-scalar-subqueries"></a>Underklöt underklöt med enkelt uttryck

En skalningsunderklädningssubquery med enkla uttryck är en korrelerad underkläde som har en SELECT-sats som inte innehåller några aggregerade uttryck. Dessa underfrågor ger inga optimeringsfördelar eftersom kompilatorn konverterar dem till ett större enkelt uttryck. Det finns ingen korrelerad kontext mellan de inre och yttre frågorna.

Här är några exempel:

**Exempel 1**

```sql
SELECT 1 AS a, 2 AS b
```

Du kan skriva om den här frågan genom att använda en underfråga för skalning med enkla uttryck för att:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Båda frågorna ger den här utdata:

```json
[
  { "a": 1, "b": 2 }
]
```

**Exempel 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Du kan skriva om den här frågan genom att använda en underfråga för skalning med enkla uttryck för att:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Frågeutdata:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Exempel 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Du kan skriva om den här frågan genom att använda en underfråga för skalning med enkla uttryck för att:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Frågeutdata:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Aggregerade skalärunderkvätningar

En aggregerad skalärsubquery är en underkvent som har en mängdfunktion i projektionen eller filtret som utvärderas till ett enda värde.

**Exempel 1:**

Här är en underkryta med ett enda mängdfunktionsuttryck i projektionen:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Frågeutdata:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Exempel 2**

Här är en underavdelning med flera mängdfunktioners uttryck:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Frågeutdata:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Exempel 3**

Här är en fråga med en aggregerad underfråga i både projektionen och filtret:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Frågeutdata:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Ett mer optimalt sätt att skriva den här frågan är att gå med på underfrågan och referera till underklyssningsaliaset i både SELECT- och WHERE-satserna. Den här frågan är effektivare eftersom du bara behöver köra underfrågan i kopplingssatsen och inte i både projektionen och filtret.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>FINNS uttryck

Azure Cosmos DB stöder EXISTS-uttryck. Detta är en aggregerad skalärsubquery inbyggd i Azure Cosmos DB SQL API. FINNS är ett booleskt uttryck som tar ett underköna uttryck och returnerar sant om underfrågan returnerar några rader. Annars returneras falskt.

Eftersom Azure Cosmos DB SQL API inte skiljer mellan booleska uttryck och andra skalsatser, kan du använda FINNS I både SELECT- och WHERE-satser. Detta är till skillnad från T-SQL, där ett booleskt uttryck (till exempel FINNS, MELLAN och IN) är begränsat till filtret.

Om underfrågan EXISTS returnerar ett enda värde som är odefinierat utvärderas EXISTS till false. Tänk till exempel på följande fråga som utvärderas till false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Om nyckelordet VALUE i föregående underfråga utelämnas utvärderas frågan till true:
```sql
SELECT EXISTS (SELECT undefined) 
```

Underfrågan omger listan med värden i den markerade listan i ett objekt. Om den valda listan inte har några värden returneras{}det enda värdet ' '. Det här värdet definieras, så EXISTS utvärderar till true.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Exempel: Skriva om ARRAY_CONTAINS och JOIN som FINNS

Ett vanligt användningsfall för ARRAY_CONTAINS är att filtrera ett dokument efter att ett objekt finns i en matris. I det här fallet kontrollerar vi om taggarna array innehåller ett objekt som heter "orange".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Du kan skriva om samma fråga som finns:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Dessutom kan ARRAY_CONTAINS bara kontrollera om ett värde är lika med ett element i en matris. Om du behöver mer komplexa filter på matrisegenskaper använder du JOIN.

Tänk på följande fråga som filtrerar `nutritionValue` baserat på enheter och egenskaper i matrisen: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

För vart och ett av dokumenten i samlingen utförs en korsprodukt med dess matriselement. Den här JOIN-åtgärden gör det möjligt att filtrera på egenskaper i matrisen. Den här frågans RU-förbrukning kommer dock att vara betydande. Om till exempel 1 000 dokument hade 100 objekt i varje matris utökas det till 1 000 x 100 (det vill än 100 000) tupplar.

Att använda EXISTS kan hjälpa till att undvika denna dyra korsprodukt:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

I det här fallet filtrerar du på matriselement i underfrågan EXISTS. Om ett matriselement matchar filtret projiceras det och EXISTS utvärderas till true.

Du kan också alias FINNS och referera till det i projektionen:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Frågeutdata:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>ARRAY-uttryck

Du kan använda ARRAY-uttrycket för att projicera resultatet av en fråga som en matris. Du kan bara använda det här uttrycket inom SELECT-satsen för frågan.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Frågeutdata:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Precis som med andra underfrågor är filter med ARRAY-uttryck möjligt.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Frågeutdata:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Matrisuttryck kan också komma efter FROM-satsen i underfrågor.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Frågeutdata:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdata](modeling-data.md)
