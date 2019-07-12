---
title: Diagnostisera och felsöka problem med frågan när du använder Azure Cosmos DB
description: Lär dig att identifiera, diagnostisera och felsöka problem med Azure Cosmos DB SQL-fråga.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 079e8677febfe6683d4f0e60a0e7ba6b06ea549d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835842"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Felsöka frågeprestanda för Azure Cosmos DB
Den här artikeln beskriver hur du identifiera, diagnostisera och felsöka problem med Azure Cosmos DB SQL-fråga. Följ anvisningarna nedan för att uppnå optimala prestanda för Azure Cosmos DB-frågor. 

## <a name="collocate-clients-in-same-azure-region"></a>Samordna klienter i samma Azure-region 
Lägsta möjliga tidsfördröjning uppnås genom att kontrollera att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB-slutpunkten. En lista över tillgängliga regioner finns i [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/#services) artikeln.

## <a name="check-consistency-level"></a>Kontrollera konsekvensnivå
[Konsekvensnivå](consistency-levels.md) kan påverka prestanda och kostnader. Kontrollera att din konsekvensnivå är lämplig för det aktuella scenariot. Mer information finns i [välja konsekvensnivå](consistency-levels-choosing.md).

## <a name="log-query-metrics"></a>Logga fråga mått
Använd `QueryMetrics` felsökning av långsam och dyr frågor. 

  * Ange `FeedOptions.PopulateQueryMetrics = true` ha `QueryMetrics` i svaret.
  * `QueryMetrics` klassen har en överbelastade `.ToString()` -funktion som kan anropas för att hämta strängrepresentation av `QueryMetrics`. 
  * Mått som kan användas för att härleda följande insikter, bland annat: 
  
      * Om någon specifik komponent i sökfrågans pipeline tog onormalt lång tid att slutföra (i ordningen på hundratals millisekunder eller mer). 

          * Titta på `TotalExecutionTime`.
          * Om den `TotalExecutionTime` av frågan är mindre än körningstid för slutpunkt till slutpunkt och tid som läggs på i klientsidan eller nätverk. Kontrollera att klienten och Azure-region är samordnad.
      
      * Om det fanns falska positiva identifieringar i dokument analyseras (om utdata dokumentantal är mycket mindre än hämtas antal dokument).  

          * Titta på `Index Utilization`.
          * `Index Utilization` = (Antal returnerade dokument / antal har lästs in dokument)
          * Om antalet returnerade dokument är mycket mindre än det antal som lästs in, sedan analyseras falska positiva identifieringar.
          * Begränsa antalet dokument som hämtas med smalare filter.  

      * Hur enskilda turer fared (se den `Partition Execution Timeline` från strängrepresentation av `QueryMetrics`). 
      * Om frågan förbrukas kostnad för hög begäran. 

Mer information finns i [hur du hämtar SQL-fråga körningsstatistik](profile-sql-api-query.md) artikeln.
      
## <a name="tune-query-feed-options-parameters"></a>Justera frågeparametrar Feed alternativ 
Kan ställa in frågeprestanda via förfrågningen [Feed alternativ](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) parametrar. Prova inställningen den nedan alternativ:

  * Ange `MaxDegreeOfParallelism` till-1 först, och som sedan jämföra prestanda över olika värden. 
  * Ange `MaxBufferedItemCount` till-1 först, och som sedan jämföra prestanda över olika värden. 
  * Ange `MaxItemCount` till-1.

När du jämför prestanda med olika värden, prova värden, till exempel 2, 4, 8, 16 och andra.
 
## <a name="read-all-results-from-continuations"></a>Läsa alla resultat från continuations
Om du tror att du inte får alla resultat, se till att tömma fortsättningen fullständigt. Behåll med andra ord, läsa resultat medan fortsättningstoken har fler dokument att ge.

Fullständigt tömning kan uppnås med något av följande mönster:

  * Fortsätta bearbetningen resultat medan fortsättning inte är tom.
  * Fortsätta att bearbeta medan frågan har fler resultat. 

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

## <a name="choose-system-functions-that-utilize-index"></a>Välj systemfunktioner som använder index
Om uttrycket kan översättas till ett antal strängvärden, kan sedan den använda indexet; Annars kan den inte. 

Här är listan över strängfunktioner som kan använda indexet: 
    
  * STARTSWITH (str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * DELSTRÄNGEN (str_expr, num_expr, num_expr) = str_expr, men endast om första num_expr är 0 
    
    Här följer några exempel på fråga: 
    
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

  * Undvika systemfunktioner i filtret (eller WHERE-satsen) som inte hanteras av indexet. Några exempel på sådana systemfunktioner är innehåller, övre och nedre.
  * Om det är möjligt kan du skriva frågor för att använda ett filter på partitionsnyckeln.
  * För att uppnå bättre undvika frågor anropa övre/lägre i filtret. Normalisera i stället skiftläge för värden vid infogning. Infoga värdet med önskad skiftläge för var och en av värden eller infoga både det ursprungliga värdet och värdet med önskad gemener och versaler. 

    Exempel:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    För det här fallet store ”JOHAN” med versaler eller lagra både ”Johan” det ursprungliga värdet och ”JOHAN”. 
    
    Om JSON data gemener och versaler är normaliserade blir frågan:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    Den andra frågan ska fungera bättre som det inte kräver att utföra omvandlingar på värden för att jämföra värden till ”JOHAN”.

DeleteFile mer information finns i [systemfunktioner](sql-query-system-functions.md) artikeln.

## <a name="check-indexing-policy"></a>Kontrollera indexering princip
Kontrollera att aktuellt [Indexeringsprincip](index-policy.md) är optimalt:

  * Se till att alla JSON-sökvägar som används i frågor som ingår i indexprincip för snabbare läsningar.
  * Uteslut sökvägar som inte används i frågor för flera högpresterande skrivningar.

Mer information finns i [hur du hanterar Indexeringsprincip](how-to-manage-indexing-policy.md) artikeln.

## <a name="spatial-data-check-ordering-of-points"></a>Spatialdata: Kontrollera sorteringen av punkter
Punkter inom en Polygon måste anges i motsols ordning. En Polygon som angetts i medurs ordning representerar inversen till regionen i den.

## <a name="optimize-join-expressions"></a>Optimera JOIN-uttryck
`JOIN` uttryck kan expandera till stora över flera produkter. När möjligt, fråga mot en mindre sökning utrymme via ett smalare filter.

Flera värden underfrågor kan optimera `JOIN` uttryck genom att skicka ut predikat efter varje select-många-uttryck i stället för när du har alla korskopplingar i den `WHERE` satsen. Ett detaljerat exempel finns i [optimera delta uttryck](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) artikeln.

## <a name="optimize-order-by-expressions"></a>Optimera ORDER BY-uttryck 
`ORDER BY` fråga prestanda sjunka om fälten är null-optimerade eller finns inte i index-principen.

  * Minska området sökning så mycket som möjligt med filter för null-optimerade fält, till exempel tid. 
  * För enskild egenskap `ORDER BY`, ta-egenskapen i index-principen. 
  * För flera egenskapen `ORDER BY` uttryck, definiera en [sammansatta index](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) på fält att sortera.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Många stora dokument som lästs in och bearbetas
Den tid och ru: er som krävs av en fråga är inte bara beroende på storleken på svaret, de är också beroende av det arbete som görs genom att fråga process-pipelinen. Tid och ru: er ökar proportionellt med mängden arbete som utförs av hela frågebearbetning pipelinen. Mer arbete utförs för stora dokument, därmed mer tid och ru: er som krävs för att läsa in och bearbeta stora dokument.

## <a name="low-provisioned-throughput"></a>Låg etablerat dataflöde
Se till att etablerade dataflödet som kan hantera arbetsbelastning. Öka RU budget för berörda samlingar.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Försök att uppgradera till den senaste SDK-versionen
Att fastställa den senaste SDK Se [SDK-nedladdning och viktig information](sql-api-sdk-dotnet.md) artikeln.

## <a name="next-steps"></a>Nästa steg
Referera till dokument nedan om hur du Mät ru: er per fråga och få statistik för körningen att finjustera dina frågor med mera:

* [Hämta körningsstatistik för SQL-fråga med hjälp av .NET SDK](profile-sql-api-query.md)
* [Justera frågeprestanda med Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Prestandatips för .NET SDK](performance-tips.md)