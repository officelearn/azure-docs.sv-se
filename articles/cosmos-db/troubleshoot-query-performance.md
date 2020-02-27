---
title: Felsök problem med frågor när du använder Azure Cosmos DB
description: Lär dig att identifiera, diagnostisera och felsöka Azure Cosmos DB problem med SQL-frågor.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 0dd3cb12c52e23a0a8acd57bf401ba68acfb9925
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623696"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Felsök problem med frågor när du använder Azure Cosmos DB

Den här artikeln vägleder dig genom en allmänt rekommenderad metod för fel sökning av frågor i Azure Cosmos DB. De steg som beskrivs i det här dokumentet bör inte betraktas som "fångst alla" för potentiella frågor, vi har inkluderat de vanligaste prestanda tipsen här. Du bör använda det här dokumentet som en start plats för fel sökning av långsamma eller kostsamma frågor i Azure Cosmos DB s SQL-API. Du kan också använda [diagnostikloggar](cosmosdb-monitor-resource-logs.md) för att identifiera frågor som är långsamma eller som använder stora mängder data flöden.

Du kan i stort sett kategorisera optimeringar av frågor i Azure Cosmos DB: optimeringar som minskar priset för begär ande enhet (RU) för den fråga och de optimeringar som bara minskar svars tiden. Genom att minska antalet RU-kostnader för en fråga kommer du snart att minska svars tiden.

I det här dokumentet används exempel som kan återskapas med hjälp av [närings](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) data uppsättningen.

## <a name="important"></a>Viktigt

- För bästa prestanda bör du följa [prestanda tipsen](performance-tips.md).
    > [!NOTE] 
    > Windows 64-bitars värd bearbetning rekommenderas för bättre prestanda. SQL-SDK: n innehåller en inbyggd ServiceInterop. dll för att parsa och optimera frågor lokalt och stöds bara på Windows x64-plattformen. För Linux och andra plattformar som inte stöds, där ServiceInterop. dll inte är tillgänglig, kommer det ytterligare ett nätverks anrop till gatewayen att hämta den optimerade frågan. 
- Cosmos DB fråga stöder inte ett minsta antal objekt.
    - Koden ska hantera en sid storlek från 0 till maximalt antal objekt
    - Antalet objekt på en sida kan och kommer att ändras utan föregående meddelande.
- Tomma sidor förväntas för frågor och kan visas när som helst. 
    - Anledningen till att tomma sidor exponeras i SDK: er är att fler möjligheter kan avbryta frågan. Det gör det också klart att SDK: n utför flera nätverks anrop.
    - Tomma sidor kan visas i befintliga arbets belastningar eftersom en fysisk partition delas i Cosmos DB. Den första partitionen har nu 0 resultat, vilket leder till den tomma sidan.
    - Tomma sidor orsakas av backend-preempting för frågan eftersom frågan tar mer än en fast tids period på Server delen för att hämta dokumenten. Om Cosmos DB blockerar en fråga returneras en fortsättnings-token som gör att frågan kan fortsätta. 
- Se till att tömma frågan helt. Titta på SDK-exemplen och Använd en while-slinga på `FeedIterator.HasMoreResults` för att tömma hela frågan.

### <a name="obtaining-query-metrics"></a>Hämta frågans mått:

När du optimerar en fråga i Azure Cosmos DB, är det första steget alltid att [Hämta frågans mått](profile-sql-api-query.md) för frågan. De är också tillgängliga via Azure Portal som visas nedan:

[![Hämta frågans mått](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

När du har hämtat frågans mått kan du jämföra antalet hämtade dokument med antalet utdata i frågan. Använd den här jämförelsen för att identifiera relevanta avsnitt som ska referera till nedan.

Antalet hämtade dokument är antalet dokument som frågan behövde läsa in. Antalet utgående dokument är antalet dokument som behövdes för frågeresultatet. Om antalet hämtade dokument är betydligt högre än antalet utgående dokument, fanns det minst en del av frågan som inte kunde använda indexet och som krävs för att göra en genomsökning.

Du kan referera till avsnittet nedan för att förstå relevanta optimeringar av frågor för ditt scenario:

### <a name="querys-ru-charge-is-too-high"></a>Frågans RU-avgift är för hög

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>Antalet hämtade dokument är betydligt större än antalet utgående dokument

- [Inkludera nödvändiga sökvägar i indexerings principen](#include-necessary-paths-in-the-indexing-policy)

- [Förstå vilka system funktioner som använder indexet](#understand-which-system-functions-utilize-the-index)

- [Frågor med både ett filter och en ORDER BY-sats](#queries-with-both-a-filter-and-an-order-by-clause)

- [Optimera KOPPLINGs uttryck med hjälp av en under fråga](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Antalet hämtade dokument är ungefär lika med antalet utgående dokument

- [Undvik kors partitions frågor](#avoid-cross-partition-queries)

- [Filter på flera egenskaper](#filters-on-multiple-properties)

- [Frågor med både ett filter och en ORDER BY-sats](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Frågans RU-avgift är acceptabel, men fördröjningen är fortfarande för hög

- [Förbättra närhet](#improve-proximity)

- [Öka det etablerade data flödet](#increase-provisioned-throughput)

- [Öka MaxConcurrency](#increase-maxconcurrency)

- [Öka MaxBufferedItemCount](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Frågor där antal hämtade dokument överskrider antalet utgående dokument

 Antalet hämtade dokument är antalet dokument som frågan behövde läsa in. Antalet utgående dokument är antalet dokument som behövdes för frågeresultatet. Om antalet hämtade dokument är betydligt högre än antalet utgående dokument, fanns det minst en del av frågan som inte kunde använda indexet och som krävs för att göra en genomsökning.

 Nedan visas ett exempel på en genomsöknings fråga som inte helt hanterades av indexet.

Fråga:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Fråga efter mått:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Antalet hämtade dokument (60 951) är betydligt större än antalet utdata per dokument (7), så den här frågan krävs för att göra en genomsökning. I det här fallet använder systemfunktionen [()](sql-query-upper.md) inte indexet.

## <a name="include-necessary-paths-in-the-indexing-policy"></a>Inkludera nödvändiga sökvägar i indexerings principen

Indexerings principen ska omfatta egenskaper som ingår i `WHERE`-satser, `ORDER BY`s satser, `JOIN`och de flesta systemfunktioner. Sökvägen som anges i index principen ska matcha (Skift läges känslig) egenskapen i JSON-dokumenten.

Om vi kör en enkel fråga på [näringsvärdes](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) data uppsättningen, ser vi en mycket lägre ru-avgift när egenskapen i `WHERE`-satsen indexeras.

### <a name="original"></a>originalspråket

Fråga:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Indexerings princip:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Avgift för ru:** 409,51 ru: er

### <a name="optimized"></a>Optimerad

Indexerings princip har uppdaterats:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Avgift för ru:** 2,98 ru: er

Du kan när som helst lägga till ytterligare egenskaper till indexerings principen, utan påverkan på Skriv tillgänglighet eller prestanda. Om du lägger till en ny egenskap i indexet används omedelbart det nya tillgängliga indexet i frågor som använder den här egenskapen. Frågan kommer att använda det nya indexet när den skapas. Resultatet blir att frågeresultaten kan vara inkonsekventa när index återställningen pågår. Om en ny egenskap är indexerad, påverkas inte frågor som bara använder befintliga index när indexet skapas på nytt. Du kan [spåra förloppet för index omvandling](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

## <a name="understand-which-system-functions-utilize-the-index"></a>Förstå vilka system funktioner som använder indexet

Om uttrycket kan översättas till ett intervall med strängvärden kan det använda indexet. Annars kan det inte göra det.

Här är en lista med strängfunktioner som kan använda indexet:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, men endast det första num_expr är 0

Några vanliga system funktioner som inte använder indexet och måste läsa in varje dokument är nedan:

| **System funktion**                     | **Idéer för optimering**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Använd Azure Search för full texts ökning                        |
| ÖVRE/NEDRE                             | I stället för att använda systemfunktionen för att normalisera data varje gång för jämförelser kan du i stället normalisera höljet vid infogning. Sedan blir en fråga som ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` bara ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| Matematiska funktioner (icke-mängder) | Om du ofta behöver beräkna ett värde i din fråga bör du lagra det här värdet som en egenskap i JSON-dokumentet. |

------

Andra delar av frågan kan fortfarande använda indexet trots att systemet inte använder indexet.

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>Frågor med både ett filter och en ORDER BY-sats

Frågor med ett filter och en `ORDER BY`-sats använder normalt ett intervall index, men de är mer effektiva om de kan hanteras från ett sammansatt index. Förutom att ändra indexerings principen bör du lägga till alla egenskaper i det sammansatta indexet i `ORDER BY`-satsen. Den här frågan kommer att se till att den använder det sammansatta indexet.  Du kan se effekten genom att köra en fråga på [närings](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) data uppsättningen.

### <a name="original"></a>originalspråket

Fråga:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Indexerings princip:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Avgift för ru:** 44,28 ru: er

### <a name="optimized"></a>Optimerad

Uppdaterad fråga (inkluderar båda egenskaperna i `ORDER BY`-satsen):

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies”
ORDER BY c.foodGroup, c._ts ASC
```

Indexerings princip har uppdaterats:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Avgift för ru:** 8,86 ru: er

## <a name="optimize-join-expressions-by-using-a-subquery"></a>Optimera KOPPLINGs uttryck med hjälp av en under fråga
Under frågor med flera värden kan optimera `JOIN` uttryck genom att push-överföra predikat efter varje Select-many-uttryck i stället för efter alla kors kopplingar i `WHERE`-satsen.

Tänk på följande fråga:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Avgift för ru:** 167,62 ru: er

I den här frågan matchar indexet alla dokument som har en tagg med namnet "mode/mode-formel", nutritionValue som är större än 0 och betjänar större belopp än 1. `JOIN`-uttrycket här utför kors produkten av alla objekt i taggar, näringsämnen och betjänar matriser för varje matchande dokument innan något filter tillämpas. `WHERE` satsen tillämpar sedan filtervärdet på varje `<c, t, n, s>` tupel.

Om ett matchande dokument exempelvis hade 10 objekt i var och en av de tre matriserna, expanderas det till 1 x 10 x 10 x 10 (d.v.s. 1 000) tupler. Om du använder under frågor här kan du filtrera ut sammanfogade mat ris objekt innan du ansluter till nästa uttryck.

Den här frågan motsvarar den tidigare en men använder under frågor:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Avgift för ru:** 22,17 ru: er

Anta att endast ett objekt i matrisen taggar matchar filtret och det finns fem objekt för båda näringsämnena och som betjänar matriser. `JOIN`-uttryck expanderas sedan till 1 x 1 x 5 x 5 = 25 objekt, i stället för 1 000 objekt i den första frågan.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Frågor där antal hämtade dokument är lika med antalet utgående dokument

Om antalet hämtade dokument är ungefär lika med antalet utgående dokument, innebär det att frågan inte behöver söka igenom många onödiga dokument. För många frågor, till exempel de som använder det översta nyckelordet, kan antalet hämtade dokument överskrida antalet utgående dokument med 1. Detta bör inte vara orsak till problem.

## <a name="avoid-cross-partition-queries"></a>Undvik kors partitions frågor

Azure Cosmos DB använder [partitionering](partitioning-overview.md) för att skala enskilda behållare som begär ande enhet och data lagring måste öka. Varje fysisk partition har ett separat och oberoende index. Om frågan har ett likhets filter som matchar din behållares partitionsnyckel, behöver du bara kontrol lera den relevanta partitionens index. Den här optimeringen minskar det totala antalet RU-frågor som frågan kräver.

Om du har ett stort antal etablerade RU-objekt (över 30 000) eller en stor mängd data som lagras (över cirka 100 GB) har du förmodligen en stor mängd behållare för att se en betydande minskning av frågan RU-avgifter.

Om vi till exempel skapar en behållare med foodGroup, behöver följande frågor bara kontrol lera en enda fysisk partition:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Dessa frågor kan också optimeras genom att inkludera partitionsnyckel i frågan:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Frågor som har intervall filter i partitionsnyckel eller som inte har några filter i partitionsnyckel måste vara "fläkt-out" och kontrol lera varje fysisk partitions index för resultat.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>Filter på flera egenskaper

Frågor med filter på flera egenskaper använder vanligt vis ett intervall index, och de är mer effektiva om de kan hanteras från ett sammansatt index. För små mängder data får den här optimeringen ingen betydande påverkan. Det kan dock vara användbart för stora mängder data. Du kan bara optimera högst ett icke-jämlikhets filter per sammansatt index. Om frågan har flera icke-jämlikhet filter, bör du välja en av dem som ska använda det sammansatta indexet. Resten fortsätter att använda intervall index. Filtret för icke-jämlikhet måste definieras sist i det sammansatta indexet. [Läs mer om sammansatta index](index-policy.md#composite-indexes)

Här följer några exempel på frågor som kan optimeras med ett sammansatt index:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Här är det relevanta sammansatta indexet:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimeringar som kortare svars tid för frågor:

I många fall kan avgiften vara acceptabel, men svars tiden för frågan är fortfarande för hög. I avsnitten nedan får du en översikt över tips för att minska svars tiden för frågor. Om du kör samma fråga flera gånger på samma data uppsättning får den samma RU-avgift varje gång. Svars tiden för frågan kan dock variera mellan frågekörningen.

## <a name="improve-proximity"></a>Förbättra närhet

Frågor som körs från en annan region än Azure Cosmos DB kontot får en högre latens än om de kördes inuti samma region. Om du till exempel körde kod på din station ära dator, bör du förvänta dig att fördröjningen ska vara flera eller hundratals (eller flera) millisekunder som är större än om frågan kom från en virtuell dator i samma Azure-region som Azure Cosmos DB. Det är enkelt att [Distribuera data i Azure Cosmos DB globalt](distribute-data-globally.md) för att se till att du kan ta med dina data närmare din app.

## <a name="increase-provisioned-throughput"></a>Öka det etablerade data flödet

I Azure Cosmos DB mäts ditt allokerade data flöde i enheter för programbegäran (RU). Anta att du har en fråga som använder 5 RUs data flöde. Om du till exempel tillhandahåller 1 000 RU kan du köra frågan 200 gånger per sekund. Om du försökte köra frågan när det inte fanns tillräckligt med data flöde är det Azure Cosmos DB att returnera ett HTTP 429-fel. Alla aktuella Core-API SDK: er kommer automatiskt att försöka utföra frågan igen efter att ha väntat en kort period. Begränsade begär Anden tar längre tid, vilket ökar det etablerade data flödet kan förbättra svars tiden. Du kan se det [totala antalet begränsade begär Anden](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) på bladet mått i Azure Portal.

## <a name="increase-maxconcurrency"></a>Öka MaxConcurrency

Parallella frågor fungerar genom att fråga flera partitioner parallellt. Data från en enskild partitionerad samling hämtas dock i serie med avseende på frågan. Det innebär att du kan justera MaxConcurrency till antalet partitioner så att du kan nå den mest utförda frågan, förutsatt att alla andra system villkor är desamma. Om du inte känner till antalet partitioner kan du ange MaxConcurrency (eller MaxDegreesOfParallelism i äldre SDK-versioner) till ett högt antal, och systemet väljer det minsta antalet partitioner, indata från användaren) som den högsta graden av parallellitet.

## <a name="increase-maxbuffereditemcount"></a>Öka MaxBufferedItemCount

Frågor är utformade för att hämta resultat när den aktuella resultat gruppen bearbetas av klienten. För hämtning bidrar till den totala tids fördröjnings förbättringen av en fråga. Att ange MaxBufferedItemCount begränsar antalet förhämtade resultat. Genom att ange det här värdet till det förväntade antalet returnerade resultat (eller ett högre tal) kan frågan få maximal nytta av för hämtning. Genom att ange värdet-1 kan systemet automatiskt bestämma antalet objekt som ska buffras.

## <a name="next-steps"></a>Nästa steg
Läs dokumenten nedan om hur du mäter ru: er per fråga, få körnings statistik för att finjustera dina frågor med mera:

* [Hämta SQL-frågekörningsmått med .NET SDK](profile-sql-api-query.md)
* [Justera frågeprestanda med Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Prestandatips för .NET SDK](performance-tips.md)
