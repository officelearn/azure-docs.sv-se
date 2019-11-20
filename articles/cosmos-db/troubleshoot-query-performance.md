---
title: Diagnostisera och Felsök problem med frågor när du använder Azure Cosmos DB
description: Lär dig att identifiera, diagnostisera och felsöka Azure Cosmos DB problem med SQL-frågor.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: fd8e80c7cd7cb71e4e0418d970cf2f328f1a3d79
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184719"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Felsöka prestanda för frågor för Azure Cosmos DB
Den här artikeln beskriver hur du identifierar, diagnostiserar och felsöker Azure Cosmos DB problem med SQL-frågor. Följ fel söknings stegen nedan för att uppnå optimala prestanda för Azure Cosmos DB frågor. 

## <a name="collocate-clients-in-same-azure-region"></a>Samordna-klienter i samma Azure-region 
Den lägsta möjliga fördröjningen uppnås genom att se till att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB slut punkten. En lista över tillgängliga regioner finns i artikeln [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/#services) .

## <a name="check-consistency-level"></a>Kontrol lera konsekvens nivå
[Konsekvens nivån](consistency-levels.md) kan påverka prestanda och kostnader. Kontrol lera att konsekvens nivån är lämplig för det aktuella scenariot. Mer information finns i [välja konsekvens nivå](consistency-levels-choosing.md).

## <a name="log-the-executed-sql-query"></a>Logga den exekverade SQL-frågan 

Du kan logga den exekverade SQL-frågan i ett lagrings konto eller tabellen Diagnostic Log. [SQL Query-loggar via diagnostikloggar](monitor-cosmos-db.md#diagnostic-settings) gör att du kan logga fördunklade-frågan i valfritt lagrings konto. På så sätt kan du titta på loggarna och Sök frågan som använder högre ru: er. Du kan senare använda aktivitets-ID: t för att matcha den faktiska frågan i QueryRuntimeStatistics. Frågan är fördunklade för säkerhets syfte och parametrarna för Frågeparametern, och deras värden i WHERE-satser skiljer sig från faktiska namn och värden. Du kan använda loggning till lagrings kontot för att behålla långsiktig kvarhållning av de exekverade frågorna.  

## <a name="log-query-metrics"></a>Logga frågornas mått

Använd `QueryMetrics` för att felsöka långsamma eller dyra frågor. 

  * Ange `FeedOptions.PopulateQueryMetrics = true` att `QueryMetrics` i svaret.
  * `QueryMetrics`-klassen har en överlagrad `.ToString()`-funktion som kan anropas för att hämta sträng representationen av `QueryMetrics`. 
  * Måtten kan användas för att härleda följande insikter, bland annat: 
  
      * Om en enskild komponent i frågans pipeline tog onormalt lång tid att slutföra (i antal hundratals millisekunder eller mer). 

          * Titta på `TotalExecutionTime`.
          * Om `TotalExecutionTime` av frågan är mindre än slut tiden till slut punkts körningen, kommer tiden att läggas på klient sidan eller nätverket. Kontrol lera att klienten och Azure-regionen är samordnad.
      
      * Om det finns falska positiva identifieringar i dokumenten som analyseras (om antalet utdata är mycket mindre än antalet hämtade dokument).  

          * Titta på `Index Utilization`.
          * `Index Utilization` = (antal returnerade dokument/antal inlästa dokument)
          * Om antalet returnerade dokument är mycket mindre än det antal som läses in, analyseras falska positiva identifieringar.
          * Begränsa antalet dokument som hämtas med snävare filter.  

      * Hur enskilda svar på resan (se `Partition Execution Timeline` från sträng representationen av `QueryMetrics`). 
      * Anger om frågan har förbrukat hög begär ande avgift. 

Mer information finns i artikeln om [hur du hämtar SQL-frågor om körnings statistik](profile-sql-api-query.md) .
      
## <a name="tune-query-feed-options-parameters"></a>Finjustera parametrar för feedalternativ 
Frågeprestanda kan finjusteras via parametrarna för [feedalternativ](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) för begäran. Prova att ange följande alternativ:

  * Ange `MaxDegreeOfParallelism` till-1 först och jämför sedan prestanda över olika värden. 
  * Ange `MaxBufferedItemCount` till-1 först och jämför sedan prestanda över olika värden. 
  * Ange `MaxItemCount` till-1.

När du jämför prestanda för olika värden kan du till exempel prova 2, 4, 8, 16 och andra värden.
 
## <a name="read-all-results-from-continuations"></a>Läsa alla resultat från fortsättningar
Om du tror att du inte får alla resultat ser du till att tömma hela fortsättningen. Du ska med andra ord fortsätta att läsa resultat så länge det finns kvar dokument att returnera för fortsättningstoken.

Du kan göra en fullständig tömning på följande sätt:

  * Fortsätt bearbetningen av resultaten medan fortsättningen inte är tom.
  * Fortsätt bearbetningen medan frågan har fler resultat. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Välja systemfunktioner som använder index
Om uttrycket kan översättas till ett intervall med strängvärden kan det använda indexet. Annars kan det inte göra det. 

Här är en lista med strängfunktioner som kan använda indexet: 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, men endast det första num_expr är 0 
    
    Här följer några exempel på frågor: 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Undvik system funktioner i filtret (eller WHERE-satsen) som inte hanteras av indexet. Några exempel på sådana system funktioner är innehåller, övre, lägre.
  * Om det är möjligt kan du skriva frågor för att använda ett filter på partitionsnyckeln.
  * För att få genomföra frågor undviker du att anropa övre/nedre i filtret. Normalisera i stället versaler med värden vid infogning. För varje värde infogar du värdet med önskat Skift läge, eller infogar både det ursprungliga värdet och värdet med det önskade Skift läget. 

    Exempel:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    I det här fallet lagrar "Johan" "Johan" eller "Johan" det ursprungliga värdet och "Johan". 
    
    Om JSON-datahöljet är normaliserat blir frågan:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    Den andra frågan är mer utförd eftersom den inte kräver omvandling av varje värde för att kunna jämföra värdena med "Johan".

Mer information om system funktioner finns i artikeln [system funktioner](sql-query-system-functions.md) .

## <a name="check-indexing-policy"></a>Kontrol lera indexerings princip
Kontrollera att den aktuella [indexeringsprincipen](index-policy.md) är optimal:

  * Se till att alla JSON-sökvägar som används i frågor ingår i indexerings principen för snabbare läsningar.
  * Uteslut sökvägar som inte används i frågor för fler utförda skrivningar.

Mer information finns i artikeln [Hantera indexerings principer](how-to-manage-indexing-policy.md) .

## <a name="spatial-data-check-ordering-of-points"></a>Spatialdata: kontrol lera sortering av punkter
Punkter inom en Polygon måste anges i motsols ordning. En Polygon som angetts i medurs ordning representerar inversen till regionen i den.

## <a name="optimize-join-expressions"></a>Optimera KOPPLINGs uttryck
`JOIN` uttryck kan utökas till stora kors produkter. När det är möjligt kan du fråga mot ett mindre Sök utrymme via ett mer smalt filter.

Under frågor med flera värden kan optimera `JOIN` uttryck genom att push-överföra predikat efter varje Select-many-uttryck i stället för efter alla kors kopplingar i `WHERE`-satsen. Ett detaljerat exempel finns i artikeln [optimera kopplings uttryck](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) .

## <a name="optimize-order-by-expressions"></a>Optimera ORDER BY-uttryck 
`ORDER BY` frågans prestanda kan påverkas om fälten är null-optimerade eller inte ingår i index principen.

  * För sparse-fält som tid, minska Sök utrymmet så mycket som möjligt med filter. 
  * Inkludera egenskap i index princip för enskild egenskap `ORDER BY`. 
  * För flera egenskaps `ORDER BY` uttryck definierar du ett [sammansatt index](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) för fält som sorteras.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Många stora dokument läses in och bearbetas
Tiden och ru: er som krävs av en fråga är inte bara beroende av svarets storlek, de är också beroende av det arbete som utförs av pipeline för bearbetning av frågor. Tid och ru: er ökar proportionellt med mängden arbete som utförs av hela kön för bearbetning av förfrågningar. Mer arbete utförs för stora dokument, så mer tid och ru: er krävs för att läsa in och bearbeta stora dokument.

## <a name="low-provisioned-throughput"></a>Låg etablerad data flöde
Se till att det etablerade data flödet kan hantera arbets belastningen. Öka RU-budgeten för påverkade samlingar.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Försök att uppgradera till den senaste SDK-versionen
För att fastställa den senaste SDK: n se artikeln om [SDK-hämtning och viktig information](sql-api-sdk-dotnet.md) .

## <a name="next-steps"></a>Nästa steg
Läs dokumenten nedan om hur du mäter ru: er per fråga, få körnings statistik för att finjustera dina frågor med mera:

* [Hämta SQL-frågekörningsmått med .NET SDK](profile-sql-api-query.md)
* [Justera frågeprestanda med Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Prestandatips för .NET SDK](performance-tips.md)
