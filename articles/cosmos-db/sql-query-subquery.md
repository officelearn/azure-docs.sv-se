---
title: SQL-underfrågor för Azure Cosmos DB
description: Läs mer om SQL underfrågor och deras vanliga användningsområden i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: tisande
ms.openlocfilehash: 4181a44e87d59d35d424a51c8fedc89523223f90
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342466"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>SQL underfråga exempel för Azure Cosmos DB

En underfråga är en fråga som bäddas in i en annan fråga. En underfråga kallas även en inre frågan eller inre Välj. Den instruktion som innehåller en underfråga kallas vanligtvis en yttre fråga.

Den här artikeln beskriver SQL underfrågor och deras vanliga användningsområden i Azure Cosmos DB. Alla exempelfrågor i det här dokumentet kan köras mot en näringsdatauppsättning som är förinstallerade på de [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Typer av underfrågor

Det finns två typer av underfrågor:

* **Korrelerade**: En underfråga som refererar till värden från den yttersta frågan. Underfrågan utvärderas en gång för varje rad som bearbetar yttre frågan.
* **Icke-korrelerade**: En underfråga som är oberoende av den yttersta frågan. Det kan köras på en egen utan att behöva det yttre frågan.

> [!NOTE]
> Azure Cosmos DB stöder endast korrelerade delfrågor.

Underfrågor kan klassificeras ytterligare baserat på antalet rader och kolumner som returnerar. Det finns tre typer:
* **tabellen**: Returnerar flera rader och flera kolumner.
* **Flera värden**: Returnerar flera rader och en enda kolumn.
* **Scalar**: Returnerar en enskild rad och en enda kolumn.

SQL-frågor i Azure Cosmos DB returnerar alltid en enda kolumn (ett enkelt värde eller ett komplicerat dokument). Därför kan endast flera värden och skalära underfrågor användas i Azure Cosmos DB. Du kan använda en underfråga i flera värden endast i FROM-satsen som en relationsdatabas uttryck. Du kan använda en skalär underfråga som ett skalärt uttryck Välj i eller WHERE-satsen eller som en relationsdatabas uttryck i FROM-satsen.

## <a name="multi-value-subqueries"></a>Underfrågor för flera värden

Flera värden underfrågor returnera en uppsättning dokument och används alltid i FROM-satsen. De används för:

* Optimera JOIN-uttryck. 
* Utvärdera dyra uttryck en gång och refererar till flera gånger.

## <a name="optimize-join-expressions"></a>Optimera JOIN-uttryck

Flera värden underfrågor kan optimera JOIN uttryck genom att skicka ut predikat efter varje select-många-uttryck i stället för när du har alla korskopplingar i WHERE-satsen.

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

Den här frågan är indexet kommer att matcha alla dokument som har en tagg med namnet ”modersmjölkersättningar formeln”. Det är ett näringsämnen objekt med ett värde mellan 0 och 10 och ett betjänar objekt med ett visst som är större än 1. JOIN-uttrycket här utför kryssprodukten av alla objekt av taggar och näringsämnen portioner matriser för varje matchande dokument innan några filter tillämpas. 

WHERE-satsen kommer därefter tillämpa filter predikatet för varje < c, t, n, s > tuppel. Till exempel om ett matchande dokument har 10 objekt i var och en av tre matriser, så den expanderas 1 x 10 x 10 x 10 (det vill säga 1 000) tupplar. Använder underfrågor här hjälper i filtrerar ut domänansluten matrisobjekt innan sammankoppling med nästa uttryck.

Den här frågan motsvarar det föregående men använder underfrågor:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Anta att endast ett objekt i matrisen taggar matchar filtret och det finns fem objekt för både näringsämnen och portioner matriser. JOIN-uttryck kommer sedan att expandera till 1 x 1 x 5 x 5 = 25 objekt, till skillnad från 1 000 objekt i den första frågan.

## <a name="evaluate-once-and-reference-many-times"></a>Utvärdera en gång och referens många gånger

Underfrågor kan optimera frågor med dyra uttryck som användardefinierade funktioner (UDF), komplexa strängar eller aritmetiska uttryck. Du kan använda en underfråga tillsammans med ett JOIN-uttryck för att utvärdera uttrycket en gång men många gånger referera till den.

Följande fråga kör en användardefinierad funktion `GetMaxNutritionValue` två gånger:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Här är en motsvarande fråga som körs i UDF bara en gång:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tänk på kryssprodukten beteendet för JOIN-uttryck. Om UDF-uttryck kan utvärderas till odefinierad, bör du kontrollera att det alltid JOIN-uttrycket skapar en enskild rad genom att returnera ett objekt från underfrågan i stället för värdet direkt.
>

Här följer ett liknande exempel som returnerar ett objekt i stället för ett värde:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Metoden är inte begränsad till UDF: er. Gäller för ett potentiellt dyra uttryck. Exempel: du kan göra samma sak med funktionen matematiska `avg`:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Efterlikna join med externa referensdata

Du kan ofta behöva refererar till statiska data som sällan ändras, till exempel enheter måttenheten eller landskoder. Det är bättre inte att duplicera sådana data för varje dokument. Undvika duplicering spara på lagring och förbättra Skrivprestandan genom att hålla dokumentet mindre. Du kan använda en underfråga för att efterlikna inre koppling semantik med en samling av referensdata.

Överväg exempelvis att den här uppsättningen med referensdata:

| **Enhet** | **Namn**            | **Multiplikatorn** | **Basenhet** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | Gram          |
| µg       | Microgram           | 1.00E-06       | Gram          |
| mg       | Milligram           | 1.00E-03       | Gram          |
| g        | Gram                | 1.00E + 00       | Gram          |
| kg       | Kilogram            | 1.00E + 03       | Gram          |
| mg       | Megagram            | 1.00E + 06       | Gram          |
| Gg       | Gigagram            | 1.00E + 09       | Gram          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E + 00       | Joule         |
| kJ       | Kilojoule           | 1.00E + 03       | Joule         |
| MJ       | Megajoule           | 1.00E + 06       | Joule         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule         |
| CAL      | Kalorier             | 1.00E + 00       | Kalorier       |
| kcal     | Kalorier             | 1.00E + 03       | Kalorier       |
| IU       | Internationella enheter |                |               |


Följande fråga imiterar sammankoppling med dessa data så att du lägger till namnet på enheten till utdata:

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

## <a name="scalar-subqueries"></a>Skalära underfrågor

Ett skalärt underfråga uttryck är en underfråga som utvärderas till ett enda värde. Värdet för skalära underfråga-uttrycket är värdet för underfrågan projektion (SELECT-satsen).  Du kan använda en skalär underfråga uttryck på många platser där ett skalärt uttryck som är giltig. Du kan exempelvis använda en skalär underfråga alla uttryck i båda Välj och WHERE-satserna.

Användningen av en skalär underfråga inte alltid till att optimera dock. Till exempel tillhandahåller skicka en skalär underfråga som ett argument till ett system eller användardefinierade funktioner inga fördelar i resursförbrukning-enhet eller svarstid.

Skalära underfrågor kan ytterligare klassificeras som:
* Uttryck för enkel skalära underfrågor
* Sammanställd skalära underfrågor

## <a name="simple-expression-scalar-subqueries"></a>Uttryck för enkel skalära underfrågor

En skalär underfråga enkel-uttrycket är en korrelerad underfråga som har en SELECT-sats som inte innehåller några mängduttryck. Dessa underfrågor ger inga optimering fördelar eftersom kompilatorn konverterar dem till en större enkelt uttryck. Det finns ingen korrelerade kontext mellan de inre och yttre frågorna.

Här följer några exempel:

**Exempel 1**

```sql
SELECT 1 AS a, 2 AS b
```

Du kan skriva om den här frågan genom att använda en enkel uttryck skalära underfråga att:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Båda frågorna ger dessa utdata:

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

Du kan skriva om den här frågan genom att använda en enkel uttryck skalära underfråga att:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Frågeresultatet visas:

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

Du kan skriva om den här frågan genom att använda en enkel uttryck skalära underfråga att:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Frågeresultatet visas:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Sammanställd skalära underfrågor

En sammanställd skalära underfråga är en underfråga som har en mängdfunktion i dess projektion eller filter som utvärderas till ett enda värde.

**Exempel 1:**

Här är en underfråga med ett enda mängdfunktion uttryck i dess projektion:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Frågeresultatet visas:

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

Här är en underfråga med flera mängdfunktion uttryck:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Frågeresultatet visas:

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

Här är en fråga med en sammanställd underfråga i både projektionen och filtret:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Frågeresultatet visas:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Ett mer optimalt sätt att skriva den här frågan är att delta i underfrågan och referera till underfråga båda Välj alias i och WHERE-satserna. Den här frågan är mer effektivt eftersom du behöver köra underfrågan endast inom join-satsen och inte i både projektion och filter.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS-uttryck

Azure Cosmos DB stöder EXISTS-uttryck. Det här är en sammanställd skalära underfråga som är inbyggda i Azure Cosmos DB SQL API. EXISTS är ett booleskt uttryck som tar en underfråga uttryck och returnerar true om underfrågan returnerar alla rader. Annars returneras false.

Eftersom Azure Cosmos DB SQL API inte skilja booleska uttryck och andra skalära uttryck, kan du använda finns i båda utvalda och WHERE-satserna. Detta skiljer sig från T-SQL, där ett booleskt uttryck (till exempel EXISTS, mellan, och i) är begränsad till filtret.

Om underfrågan EXISTS returnerar ett värde som innehåller en odefinierad, finns utvärderas till false. Till exempel överväga följande fråga som utvärderas till false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Om du utelämnar nyckelordet värdet i föregående underfrågan ska frågan utvärderas till SANT:
```sql
SELECT EXISTS (SELECT undefined) 
```

Underfrågan infogas i listan med värden i den markerade listan i ett objekt. Om den markerade listan har inga värden, underfrågan returnerar det enskilda värdet '{}'. Det här värdet har definierats så EXISTS utvärderas till SANT.

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Exempel: Skriva om ARRAY_CONTAINS och ANSLUTIT som EXISTS

Ett vanligt användningsfall för ARRAY_CONTAINS är att filtrera ett dokument efter förekomsten av ett objekt i en matris. I det här fallet kontrollerar vi om taggar matrisen innehåller ett objekt med namnet ”orange”.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Du kan skriva om samma fråga om du vill använda EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

ARRAY_CONTAINS kan dessutom bara kontrollera om ett värde är lika med alla element i en matris. Om du behöver fler komplexa filter på matrisen egenskaper, använder du koppling.

Överväg följande fråga som filtrerar baserat på enheterna och `nutritionValue` egenskaper i matrisen: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

För varje dokument i samlingen utförs en kryssprodukten med dess matriselement. Den här JOIN-åtgärd gör det möjligt att filtrera egenskaper i matrisen. Den här frågan RU förbrukning kommer dock vara betydande. Exempelvis om 1 000 dokument har 100 objekt i varje matris, så den expanderas till 1 000 x 100 (det vill säga 100 000) tupplar.

Med hjälp av EXISTS hjälper dig för att undvika den här dyra kryssprodukten:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

I det här fallet, filtrera på matriselement inom EXISTS-underfråga. Om ett matriselement matchar filtret, sedan du projicera och EXISTS utvärderas till SANT.

Du kan också alias EXISTS och referera till det i projektionen:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Frågeresultatet visas:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Matrisuttryck

Du kan använda matrisuttryck projicera resultatet av en fråga som en matris. Du kan använda det här uttrycket i SELECT-satsen för frågan.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Frågeresultatet visas:

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

Precis som med andra underfrågor är filter med matrisuttryck möjliga.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Frågeresultatet visas:

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

Matris uttryck kan också komma efter FROM-satsen i underfrågor.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Frågeresultatet visas:

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

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelldokumentdata](modeling-data.md)
