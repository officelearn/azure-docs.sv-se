---
title: Felsöka frågeproblem när du använder Azure Cosmos DB
description: Lär dig hur du identifierar, diagnostiserar och felsöker Azure Cosmos DB SQL-frågeproblem.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 4a8b61f3719a60af567d10f8839987e613babc9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870462"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Felsöka frågeproblem när du använder Azure Cosmos DB

Den här artikeln går igenom en allmän rekommenderad metod för felsökning av frågor i Azure Cosmos DB. Även om du inte bör överväga de steg som beskrivs i den här artikeln ett fullständigt försvar mot potentiella frågeproblem, har vi inkluderat de vanligaste prestandatipsen här. Du bör använda den här artikeln som en startplats för felsökning av långsamma eller dyra frågor i Azure Cosmos DB core (SQL) API. Du kan också använda [diagnostikloggar](cosmosdb-monitor-resource-logs.md) för att identifiera frågor som är långsamma eller som förbrukar betydande mängder dataflöde.

Du kan i stort sett kategorisera frågeoptimeringar i Azure Cosmos DB:

- Optimeringar som minskar frågans avgift för begäranhet (RU)
- Optimeringar som bara minskar svarstiden

Om du minskar RU-avgiften för en fråga minskar du nästan säkert svarstiden också.

Den här artikeln innehåller exempel som du kan återskapa med hjälp av [näringsdatauppsättningen.](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

## <a name="common-sdk-issues"></a>Vanliga SDK-problem

- För bästa prestanda, följ [prestandatipsen](performance-tips.md).
    > [!NOTE]
    > För bättre prestanda rekommenderar vi windows 64-bitars värdbearbetning. SQL SDK innehåller en inbyggd ServiceInterop.dll för att tolka och optimera frågor lokalt. ServiceInterop.dll stöds endast på Windows x64-plattformen. För Linux och andra plattformar som inte stöds där ServiceInterop.dll inte är tillgängligt kommer ytterligare ett nätverksanrop att göras till gatewayen för att få den optimerade frågan.
- Du kan `MaxItemCount` ange en för dina frågor men du kan inte ange ett minsta antal objekt.
    - Koden ska hantera valfri sidstorlek, `MaxItemCount`från noll till .
    - Antalet objekt på en sida kommer alltid att `MaxItemCount`vara mindre än det angivna . Men `MaxItemCount` är strikt ett maximum och det kan finnas färre resultat än detta belopp.
- Ibland kan frågor ha tomma sidor även när det finns resultat på en framtida sida. Orsaker till detta kan vara:
    - SDK kan göra flera nätverkssamtal.
    - Frågan kan ta lång tid att hämta dokumenten.
- Alla frågor har en fortsättningstoken som gör att frågan kan fortsätta. Var noga med att tömma frågan helt. Titta på SDK-exemplen `while` och `FeedIterator.HasMoreResults` använd en loop för att tömma hela frågan.

## <a name="get-query-metrics"></a>Hämta frågemått

När du optimerar en fråga i Azure Cosmos DB är det första steget alltid att [hämta frågemåtten](profile-sql-api-query.md) för din fråga. Dessa mått är också tillgängliga via Azure-portalen:

[![Hämta frågemått](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

När du har hämtat frågemåtten jämför du antalet hämtade dokument med antalet utdatadokument för frågan. Använd den här jämförelsen för att identifiera relevanta avsnitt som ska granskas i den här artikeln.

Antalet hämtade dokument är antalet dokument som frågan behövde läsas in. Antalet utdatadokument är antalet dokument som behövdes för resultatet av frågan. Om antalet hämtade dokument är betydligt högre än antalet utdatadokument fanns det minst en del av frågan som inte kunde använda indexet och som behövdes för att göra en genomsökning.

Se följande avsnitt för att förstå relevanta frågeoptimeringar för ditt scenario.

### <a name="querys-ru-charge-is-too-high"></a>Frågans RU-laddning är för hög

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Antalet hämtade dokument är betydligt högre än antalet utdatadokument

- [Inkludera nödvändiga sökvägar i indexeringsprincipen.](#include-necessary-paths-in-the-indexing-policy)

- [Förstå vilka systemfunktioner som använder indexet.](#understand-which-system-functions-use-the-index)

- [Förstå vilka aggregerade frågor som använder indexet.](#understand-which-aggregate-queries-use-the-index)

- [Ändra frågor som har både ett filter och en ORDER BY-sats.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optimera JOIN-uttryck med hjälp av en underkryta.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Antalet hämtade dokument är ungefär lika med antalet utdatadokument

- [Undvik korspartitionsfrågor.](#avoid-cross-partition-queries)

- [Optimera frågor som har filter på flera egenskaper.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Ändra frågor som har både ett filter och en ORDER BY-sats.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Frågans RU-laddning är acceptabel men latensen är fortfarande för hög

- [Förbättra närheten.](#improve-proximity)

- [Öka etablerat dataflöde.](#increase-provisioned-throughput)

- [Öka MaxConcurrency.](#increase-maxconcurrency)

- [Öka MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Frågor där hämtat antal dokument överskrider antalet utdatadokument

 Antalet hämtade dokument är antalet dokument som frågan behövde läsas in. Antalet utdatadokument är antalet dokument som behövdes för resultatet av frågan. Om antalet hämtade dokument är betydligt högre än antalet utdatadokument fanns det minst en del av frågan som inte kunde använda indexet och som behövdes för att göra en genomsökning.

Här är ett exempel på skanningsfråga som inte helt betjänades av indexet:

Fråga:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Frågemått:

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

Antalet hämtade dokument (60 951) är betydligt högre än antalet utdatadokument (7), så den här frågan behövs för att göra en genomsökning. I det här fallet använder inte systemfunktionen [UPPER()](sql-query-upper.md) indexet.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Inkludera nödvändiga sökvägar i indexeringsprincipen

Indexeringsprincipen bör omfatta alla `WHERE` egenskaper `ORDER BY` som ingår `JOIN`i satser, satser och de flesta systemfunktioner. Sökvägen som anges i indexprincipen ska matcha (skiftlägeskänslig) egenskapen i JSON-dokumenten.

Om du kör en enkel fråga om [näringsdatauppsättningen](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) observerar du en `WHERE` mycket lägre RU-avgift när egenskapen i satsen indexeras:

#### <a name="original"></a>Ursprungliga

Fråga:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Indexeringsprincip:

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

**RU avgift:** 409,51 RU

#### <a name="optimized"></a>Optimerad

Uppdaterad indexeringsprincip:

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

**RU avgift:** 2,98 RU

Du kan lägga till egenskaper i indexeringsprincipen när som helst, utan att påverka skrivtillgängligheten eller resultatet. Om du lägger till en ny egenskap i indexet används omedelbart det nya tillgängliga indexet i frågor som använder egenskapen. Frågan kommer att använda det nya indexet medan det byggs. Så frågeresultat kan vara inkonsekventa medan index återskapa pågår. Om en ny egenskap indexeras påverkas inte frågor som endast använder befintliga index under indexombyggnaden. Du kan [spåra förlopp för indexomvandling](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-system-functions-use-the-index"></a>Förstå vilka systemfunktioner som använder indexet

Om ett uttryck kan översättas till ett intervall med strängvärden kan det använda indexet. Annars kan det inte.

Här är listan över några vanliga strängfunktioner som kan använda indexet:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- DELSTRÄNG(str_expr, num_expr, num_expr) = str_expr, men bara om den första num_expr är 0

Följande är några vanliga systemfunktioner som inte använder indexet och måste läsa in varje dokument:

| **Systemfunktion**                     | **Idéer för optimering**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Använd Azure Search för fulltextsökning.                        |
| ÖVRE/NEDRE                             | I stället för att använda systemfunktionen för att normalisera data för jämförelser normaliserar du höljet vid insättning. En fråga ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` ```SELECT * FROM c WHERE c.name = 'BOB'```som blir . |
| Matematiska funktioner (icke-aggregat) | Om du behöver beräkna ett värde ofta i frågan bör du överväga att lagra värdet som en egenskap i JSON-dokumentet. |

------

Andra delar av frågan kan fortfarande använda indexet även om systemfunktionerna inte gör det.

### <a name="understand-which-aggregate-queries-use-the-index"></a>Förstå vilka aggregerade frågor som använder indexet

I de flesta fall använder aggregerade systemfunktioner i Azure Cosmos DB indexet. Beroende på filter eller ytterligare satser i en aggregerad fråga kan frågemotorn dock behövas för att läsa in ett stort antal dokument. Frågemotorn tillämpar vanligtvis likhets- och intervallfilter först. När du har använt dessa filter kan frågemotorn utvärdera ytterligare filter och tillgripa inläsning av återstående dokument för att beräkna aggregationen om det behövs.

Med tanke på dessa två exempelfrågor blir frågan `CONTAINS` med både likhets- och systemfunktionsfilter `CONTAINS` i allmänhet effektivare än en fråga med bara ett systemfunktionsfilter. Detta beror på att likhetsfiltret används först och använder indexet `CONTAINS` innan dokument behöver läsas in för det dyrare filtret.

Fråga med `CONTAINS` endast filter - högre RU avgift:

```sql
SELECT COUNT(1) FROM c WHERE CONTAINS(c.description, "spinach")
```

Fråga med både `CONTAINS` likhetsfilter och filter - lägre RU-laddning:

```sql
SELECT AVG(c._ts) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Här är ytterligare exempel på aggregerade frågor som inte helt kommer att använda indexet:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Frågor med systemfunktioner som inte använder indexet

Du bör läsa den relevanta [systemfunktionens sida](sql-query-system-functions.md) för att se om den använder indexet.

```sql
SELECT MAX(c._ts) FROM c WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Aggregera frågor med användardefinierade funktioner(UDF)

```sql
SELECT AVG(c._ts) FROM c WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Frågor med GROUP BY

RU-laddningen `GROUP BY` av kommer att öka i `GROUP BY` takt med att kardinaliteten hos egenskaperna i klausulen ökar. I det här exemplet måste frågemotorn `c.foodGroup = "Sausages and Luncheon Meats"` läsa in alla dokument som matchar filtret så att RU-laddningen förväntas vara hög.

```sql
SELECT COUNT(1) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" GROUP BY c.description
```

Om du planerar att köra samma mängdfrågor ofta kan det vara mer effektivt att skapa en materialiserad vy i realtid med [Azure Cosmos DB-ändringsflödet](change-feed.md) än att köra enskilda frågor.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Ändra frågor som har både ett filter och en ORDER BY-sats

Även om frågor som har `ORDER BY` ett filter och en sats normalt använder ett intervallindex, blir de mer effektiva om de kan visas från ett sammansatt index. Förutom att ändra indexeringsprincipen bör du lägga till alla `ORDER BY` egenskaper i kompositindexet i satsen. Den här ändringen av frågan säkerställer att det använder det sammansatta indexet.  Du kan observera effekten genom att [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) köra en fråga på näringsdatauppsättningen:

#### <a name="original"></a>Ursprungliga

Fråga:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Indexeringsprincip:

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

**RU-laddning:** 44.28 Ru:er

#### <a name="optimized"></a>Optimerad

Uppdaterad fråga (innehåller båda `ORDER BY` egenskaperna i satsen):

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Uppdaterad indexeringsprincip:

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

**RU avgift:** 8,86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Optimera JOIN-uttryck med hjälp av en underkryta
Underfrågor med flera värden `JOIN` kan optimera uttryck genom att trycka predikat efter varje markeringsna-många-uttryck i stället för att alla korskopplingar i `WHERE` satsen har gått samman.

Tänk på den här frågan:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU avgift:** 167,62 RU

För den här frågan matchar indexet alla dokument som har en tagg med namnet "modersmjölksersättning", nutritionValue större än 0 och serveringsbelopp som är större än 1. Uttrycket `JOIN` här kommer att utföra korsprodukten av alla objekt av taggar, näringsämnen och portioner matriser för varje matchande dokument innan något filter tillämpas. Satsen `WHERE` kommer sedan att tillämpa filtret predikat på varje `<c, t, n, s>` tuppel.

Om ett matchande dokument till exempel har 10 objekt i var och en av de tre matriserna utökas det till 1 x 10 x 10 x 10 (det villan 1 000). Användningen av underfrågor här kan hjälpa till att filtrera bort kopplade matrisobjekt innan du går med i nästa uttryck.

Den här frågan motsvarar den föregående men använder underfrågor:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU avgift:** 22,17 RU

Anta att endast ett objekt i taggmatrisen matchar filtret och att det finns fem objekt för både näringsämnen och portioner. Uttrycken `JOIN` utökas till 1 x 1 x 5 x 5 = 25 objekt, jämfört med 1 000 objekt i den första frågan.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Frågor där hämtat antal dokument är lika med antalet utdatadokument

Om antalet hämtade dokument är ungefär lika med antalet utdatadokument behövde frågan inte söka igenom många onödiga dokument. För många frågor, till exempel de som använder nyckelordet TOP, kan antalet hämtade dokument överskrida antalet utdatadokument med 1. Du behöver inte oroa dig för det här.

### <a name="avoid-cross-partition-queries"></a>Undvik korspartitionsfrågor

Azure Cosmos DB använder [partitionering](partitioning-overview.md) för att skala enskilda behållare när begärandeenheten och datalagringsbehoven ökar. Varje fysisk partition har ett separat och oberoende index. Om frågan har ett likhetsfilter som matchar behållarens partitionsnyckel måste du bara kontrollera den relevanta partitionens index. Den här optimeringen minskar det totala antalet ru:er som frågan kräver.

Om du har ett stort antal etablerade ru:er (mer än 30 000) eller en stor mängd data som lagras (mer än cirka 100 GB) har du förmodligen en tillräckligt stor behållare för att se en betydande minskning av fråge-RU-avgifter.

Om du till exempel skapar en behållare med partitionsnyckeln foodGroup måste följande frågor bara kontrollera en enda fysisk partition:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Dessa frågor skulle också optimeras genom tillägg av partitionsnyckeln i frågan:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Frågor som har intervallfilter på partitionsnyckeln, eller som inte har några filter på partitionsnyckeln, måste kontrollera varje fysisk partitions index för resultat:

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Optimera frågor som har filter på flera egenskaper

Även om frågor som har filter på flera egenskaper normalt använder ett intervallindex, blir de mer effektiva om de kan visas från ett sammansatt index. För små mängder data har den här optimeringen ingen betydande inverkan. Det kan dock vara användbart för stora mängder data. Du kan bara optimera, högst en icke-equality filter per sammansatt index. Om frågan har flera icke-likhetsfilter väljer du ett av dem som använder kompositindexet. Resten fortsätter att använda intervallindex. Filtret för icke-likhet måste definieras sist i kompositindexet. [Läs mer om sammansatta index](index-policy.md#composite-indexes).

Här är några exempel på frågor som kan optimeras med ett sammansatt index:

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

## <a name="optimizations-that-reduce-query-latency"></a>Optimeringar som minskar frågesvarstiden

I många fall kan RU-avgiften vara acceptabel när frågefördröjningen fortfarande är för hög. I följande avsnitt ges en översikt över tips för att minska frågefördröjningen. Om du kör samma fråga flera gånger på samma datauppsättning har den samma RU-laddning varje gång. Men frågefördröjningen kan variera mellan frågekörningar.

### <a name="improve-proximity"></a>Förbättra närheten

Frågor som körs från en annan region än Azure Cosmos DB-kontot har högre svarstid än om de kördes inom samma region. Om du till exempel kör kod på din stationära dator bör du förvänta dig att svarstiden är tiotals eller hundratals millisekunder högre (eller fler) än om frågan kom från en virtuell dator inom samma Azure-region som Azure Cosmos DB. Det är enkelt att [globalt distribuera data i Azure Cosmos DB](distribute-data-globally.md) för att säkerställa att du kan föra dina data närmare din app.

### <a name="increase-provisioned-throughput"></a>Öka etablerat dataflöde

I Azure Cosmos DB mäts ditt etablerade dataflöde i Begärandeenheter (RU:er). Anta att du har en fråga som förbrukar 5 ru:er av dataflöde. Om du till exempel etablerar 1 000 ru: er kan du köra frågan 200 gånger per sekund. Om du försökte köra frågan när det inte fanns tillräckligt med dataflöde tillgängligt returnerar Azure Cosmos DB ett HTTP 429-fel. Alla de aktuella core-API-SDK:erna (Core) försöker automatiskt igen med den här frågan efter att ha väntat en kort stund. Begränsade begäranden tar längre tid, så att öka etablerat dataflöde kan förbättra frågefördröjningen. Du kan observera det [totala antalet begränsade begäranden](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) på **metrics-bladet** i Azure-portalen.

### <a name="increase-maxconcurrency"></a>Öka MaxConcurrency

Parallella frågor fungerar genom att fråga flera partitioner parallellt. Men data från en enskild partitionerad samling hämtas seriellt med avseende på frågan. Så om du ställer in MaxConcurrency till antalet partitioner har du den bästa chansen att uppnå den mest högpresterande frågan, förutsatt att alla andra systemvillkor förblir desamma. Om du inte vet antalet partitioner kan du ställa in MaxConcurrency (eller MaxDegreesOfParallelism i äldre SDK-versioner) till ett högt antal. Systemet kommer att välja det minsta (antal partitioner, användarindata) som den maximala graden av parallellism.

### <a name="increase-maxbuffereditemcount"></a>Öka MaxBufferedItemCount

Frågor är utformade för att förhämtning av resultat medan den aktuella batchen med resultat bearbetas av klienten. Förhämtning bidrar till att förbättra den övergripande svarstiden för en fråga. Om du ställer in MaxBufferedItemCount begränsas antalet förhämtningsresultat. Om du anger det här värdet på det förväntade antalet returnerade resultat (eller ett högre tal) kan frågan få mest nytta av förhämtning. Om du anger det här värdet till -1 bestämmer systemet automatiskt antalet objekt som ska buffras.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för information om hur du mäter ru:er per fråga, hämtar körningsstatistik för att ställa in dina frågor med mera:

* [Hämta mått för SQL-frågekörning med hjälp av .NET SDK](profile-sql-api-query.md)
* [Justera frågeprestanda med Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Prestandatips för .NET SDK](performance-tips.md)
