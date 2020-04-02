---
title: Prestandatips för Azure Cosmos DB för Java
description: Lär dig alternativ för klientkonfiguration för att förbättra Azure Cosmos-databasens prestanda
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: a20b7d91a927d48a14812110ca714491cd726071
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548780"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Prestandatips för Azure Cosmos DB och Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB är en snabb och flexibel distribuerad databas som skalas sömlöst med garanterad svarstid och dataflöde. Du behöver inte göra större arkitekturändringar eller skriva komplex kod för att skala databasen med Azure Cosmos DB. Att skala upp och ned är lika enkelt som att ringa ett enda API-anrop. Mer information finns i [hur du etablerar behållardataflöde](how-to-provision-container-throughput.md) eller [hur du etablerar databasdataflöde](how-to-provision-database-throughput.md). Men eftersom Azure Cosmos DB nås via nätverksamtal finns det optimering på klientsidan som du kan göra för att uppnå toppprestanda när du använder [SQL Java SDK](documentdb-sdk-java.md).

Så om du frågar "Hur kan jag förbättra min databas prestanda?" överväga följande alternativ:

## <a name="networking"></a>Nätverk
<a id="direct-connection"></a>

1. **Anslutningsläge: Använd direkthttps://0-läge**

    Hur en klient ansluter till Azure Cosmos DB har stor inverkan på prestandan, särskilt de svarstider som uppstår på klientsidan. Det finns en nyckelkonfigurationsinställning för att konfigurera klienten [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) – [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  De två tillgängliga ConnectionModes är:

   1. [Gateway (standard)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [Direkthttps://1.00](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      Gateway-läge stöds på alla SDK-plattformar och är den konfigurerade standardinställningen.  Om ditt program körs i ett företagsnätverk med strikta brandväggsbegränsningar är Gateway det bästa valet eftersom den använder standard-HTTPS-porten och en enda slutpunkt. Prestanda kompromissen är dock att Gateway-läge innebär ytterligare ett nätverkshopp varje gång data läss eller skrivs till Azure Cosmos DB. På grund av detta ger DirectHttps-läget bättre prestanda på grund av färre nätverkshopp. 

      Java SDK använder HTTPS som transportprotokoll. HTTPS använder TLS för inledande autentisering och kryptering av trafik. När du använder Java SDK behöver endast HTTPS-port 443 vara öppen. 

      ConnectionMode konfigureras under konstruktionen av DocumentClient-instansen med parametern ConnectionPolicy. 

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      ![Illustration av Azure Cosmos DB-anslutningsprincipen](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Samlokaliseringsklienter i samma Azure-region för prestanda**

    När det är möjligt placerar du alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos-databasen. För en ungefärlig jämförelse, anrop till Azure Cosmos DB inom samma region slutföras inom 1-2 ms, men svarstiden mellan väst- och östkusten i USA är >50 ms. Den här svarstiden kan troligen variera från begäran till begäran beroende på vilken väg som hämtas av begäran när den skickas från klienten till Azure-datacentergränsen. Lägsta möjliga svarstid uppnås genom att se till att anropandet finns inom samma Azure-region som den etablerade Azure Cosmos DB-slutpunkten. En lista över tillgängliga regioner finns i [Azure Regions](https://azure.microsoft.com/regions/#services).

    ![Illustration av Azure Cosmos DB-anslutningsprincipen](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-användning
1. **Installera den senaste SDK**

    Azure Cosmos DB SDK:er förbättras ständigt för att ge bästa prestanda. Se [Azure Cosmos DB SDK-sidor](documentdb-sdk-java.md) för att fastställa de senaste SDK- och granskningsförbättringarna.
2. **Använda en Azure Cosmos DB-klient för singleton-azure cosmos för ditt programs livstid**

    Varje [DocumentClient-instans](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) är trådsäker och utför effektiv anslutningshantering och adresscachelagring när du arbetar i direktläge. För att möjliggöra effektiv anslutningshantering och bättre prestanda genom DocumentClient rekommenderar vi att du använder en enda instans av DocumentClient per AppDomain under programmets livstid.

   <a id="max-connection"></a>
3. **Öka MaxPoolSize per värd när du använder gateway-läge**

    Azure Cosmos DB-begäranden görs via HTTPS/REST när gateway-läge används och omfattas av standardanslutningsgränsen per värdnamn eller IP-adress. Du kan behöva ange MaxPoolSize till ett högre värde (200-1000) så att klientbiblioteket kan använda flera samtidiga anslutningar till Azure Cosmos DB. I Java SDK är standardvärdet för [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) 100. Använd [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) för att ändra värdet.

4. **Justera parallella frågor för partitionerade samlingar**

    Azure Cosmos DB SQL Java SDK version 1.9.0 och högre stöder parallella frågor, som gör att du kan fråga en partitionerad samling parallellt. Mer information finns i [kodexempel](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) relaterade till att arbeta med SDK:erna. Parallella frågor är utformade för att förbättra frågefördröjning och dataflöde över deras seriella motsvarighet.

    (a) ***Tuning setMaxDegreeOfParallelism\: *** Parallella frågor fungerar genom att fråga flera partitioner parallellt. Data från en enskild partitionerad samling hämtas dock seriellt med avseende på frågan. Så, använd [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) för att ställa in antalet partitioner som har maximal chans att uppnå den mest högpresterande frågan, förutsatt att alla andra systemvillkor förblir desamma. Om du inte vet antalet partitioner kan du använda setMaxDegreeOfParallelism för att ställa in ett högt antal, och systemet väljer det minsta (antal partitioner, användarindata) som maximal grad av parallellism. 

    Det är viktigt att notera att parallella frågor ger de bästa fördelarna om data är jämnt fördelade över alla partitioner med avseende på frågan. Om den partitionerade samlingen är partitionerad på ett sådant sätt att alla eller en majoritet av de data som returneras av en fråga är koncentrerad till några partitioner (en partition i värsta fall), då skulle frågans prestanda flaskhalsas av dessa partitioner.

    (b) ***Tuning setMaxBufferedItemCount\: *** Parallel-frågan är utformad för att förmåla resultat medan den aktuella batchen med resultat bearbetas av klienten. Förhämtningen hjälper till att förbättra en fråga totalt sett. setMaxBufferedItemCount begränsar antalet förhämtningsresultat. Genom att ställa [in setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) till det förväntade antalet returnerade resultat (eller ett högre tal) gör detta att frågan kan få maximal nytta av förhämtning.

    Förhämtning fungerar på samma sätt oavsett MaxDegreeOfParallelism, och det finns en enda buffert för data från alla partitioner.  

5. **Implementera backoff vid intervallen getRetryAfterInMilliseconds**

    Under prestandatestning bör du öka belastningen tills en liten mängd begäranden begränsas. Om det begränsas ska klientprogrammet backa av på gasreglaget för det serverspecificerade återförsöksintervallet. Med respekt för backoff säkerställer att du tillbringar minimal tid att vänta mellan försök. Stöd för återförsökspolicy ingår i version 1.8.0 och högre från [Java SDK](documentdb-sdk-java.md). Mer information finns i [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Skala ut din klient-arbetsbelastning**

    Om du testar på höga dataflödesnivåer (>50 000 RU/s) kan klientprogrammet bli flaskhalsen på grund av att maskinen begränsar cpu- eller nätverksanvändningen. Om du når den här punkten kan du fortsätta att driva Azure Cosmos DB-kontot vidare genom att skala ut dina klientprogram över flera servrar.

7. **Använd namnbaserad adressering**

    Använd namnbaserad adressering, där länkar `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`har formatet ,\_i stället för `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` SelfLinks ( self), som har formatet för att undvika att hämta ResourceIds av alla resurser som används för att konstruera länken. Eftersom dessa resurser återskapas (eventuellt med samma namn) kanske det inte hjälper att cachelagring av dessa.

   <a id="tune-page-size"></a>
8. **Justera sidstorleken för frågor/läsflöden för bättre prestanda**

    När du läser en massläsning av dokument med hjälp av läsflödesfunktioner (till exempel [readDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) eller när du utfärdar en SQL-fråga returneras resultaten på ett segmenterat sätt om resultatuppsättningen är för stor. Som standard returneras resultaten i segment med 100 objekt eller 1 MB, beroende på vilken gräns som uppnås först.

    Om du vill minska antalet nätverksrundningsturer som krävs för att hämta alla tillämpliga resultat kan du öka sidstorleken med hjälp av begäranshuvudet för [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) till upp till 1 000. I de fall där du behöver visa endast ett fåtal resultat, till exempel om ditt användargränssnitt eller program-API returnerar endast 10 resultat per gång, kan du också minska sidstorleken till 10 för att minska dataflödet som förbrukas för läsningar och frågor.

    Du kan också ange sidstorleken med [metoden setPageSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Indexeringsprincip
 
1. **Utesluta sökvägar som inte används från indexering för att få snabbare skrivning**

    Azure Cosmos DB:s indexeringsprincip gör att du kan ange vilka dokumentsökvägar som ska inkluderas eller uteslutas från indexering genom att utnyttja indexeringssökvägar[(setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) och [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)). Användningen av indexeringsvägar kan erbjuda förbättrad skrivprestanda och lägre indexlagring för scenarier där frågemönstren är kända i förväg, eftersom indexeringskostnaderna är direkt korrelerade till antalet unika sökvägar som indexeras.  Följande kod visar till exempel hur du utesluter en hel del av dokumenten (även om det finns en del av dokumenten. ett underträd) från indexering med jokertecknet "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Mer information finns i [Azure Cosmos DB-indexeringsprinciper](indexing-policies.md).

## <a name="throughput"></a>Dataflöde
<a id="measure-rus"></a>

1. **Mäta och justera för lägre begäranheter/andra användning**

    Azure Cosmos DB erbjuder en omfattande uppsättning databasåtgärder, inklusive relations- och hierarkiska frågor med UDF:er, lagrade procedurer och utlösare – alla som arbetar med dokumenten i en databassamling. Den kostnad som hör till var och en av dessa operationer varierar baserat på vilken CPU, vilka IO-resurser och hur mycket minne som krävs för att slutföra operationen. I stället för att tänka på och hantera maskinvaruresurser kan du tänka dig en begäranhet (RU) som ett enda mått för de resurser som krävs för att utföra olika databasåtgärder och serva en programbegäran.

    Dataflöde etableras baserat på antalet [begärandeenheter](request-units.md) som angetts för varje behållare. Förbrukning av begäranden enhet utvärderas som en hastighet per sekund. Program som överskrider den etablerade enhetssatsen för begäran för sin behållare är begränsade tills hastigheten sjunker under den etablerade nivån för behållaren. Om ditt program kräver en högre nivå av dataflöde kan du öka dataflödet genom att etablera ytterligare begäranheter. 

    Komplexiteten i en fråga påverkar hur många begärandeenheter som förbrukas för en åtgärd. Antalet predikat, typ av predikat, antal UDF:er och storleken på källdatauppsättningen påverkar alla kostnaden för frågeåtgärder.

    Om du vill mäta omkostnaderna för en åtgärd (skapa, uppdatera eller ta bort) inspekterar [du x-ms-begäran-avgiftshuvudet](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (eller motsvarande RequestCharge-egenskap i [ResourceResponse\<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) eller [FeedResponse\<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) för att mäta antalet begärandeenheter som förbrukas av dessa åtgärder.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Den begärandeavgift som returneras i det här huvudet är en bråkdel av det etablerade dataflödet. Om du till exempel har etablerat 2000 RU/s och om föregående fråga returnerar 1000 1KB-dokument, är kostnaden för operationen 1000. Som sådan, inom en sekund, servern respekterar endast två sådana förfrågningar innan kurs begränsa efterföljande begäranden. Mer information finns i [Begär enheter](request-units.md) och [räknaren för begäranhet](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Hantera hastighetsbegränsning/begäranden för stor**

    När en klient försöker överskrida det reserverade dataflödet för ett konto, finns det ingen prestandaförsämring på servern och ingen användning av dataflödeskapacitet utöver den reserverade nivån. Servern avslutar begäran i förebyggande syfte med RequestRateTooLarge (HTTP-statuskod 429) och returnerar [x-ms-retry-after-ms-huvudet](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) som anger hur lång tid, i millisekunder, som användaren måste vänta innan begäran upprepas.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Alla SDK:er fångar underförstått det här svaret, respekterar det serverinderade återförsöks-efter-huvudet och försöker igen med begäran. Om inte ditt konto används samtidigt av flera klienter, kommer nästa återförsök att lyckas.

    Om du har mer än en klient som är kumulativt verksamt över begäranden, kanske det för närvarande inte räcker med att antalet standardförsök är 9 internt av klienten. I det här fallet genererar klienten en [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) med statuskod 429 till programmet. Standardantalet för återförsök kan ändras med hjälp av [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) i [ConnectionPolicy-instansen.](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) Som standard returneras DocumentClientException med statuskod 429 efter en sammanlagd väntetid på 30 sekunder om begäran fortsätter att fungera över begäranden. Detta inträffar även när det aktuella antalet försök på nytt är mindre än antalet försök per max, oavsett om det är standardvärdet 9 eller ett användardefinierat värde.

    Även om det automatiserade återförsöksbeteendet bidrar till att förbättra återhämtningen och användbarheten för de flesta program, kan det gå i strid när prestandariktmärkena ändras, särskilt när svarstiden mäts.  Den klient observerade svarstiden kommer att öka om experimentet träffar serverbegränsningen och gör att klienten SDK tyst försöker igen. För att undvika fördröjningsstopp under prestandaexperiment mäter du debiteringen som returneras av varje åtgärd och säkerställer att begäranden fungerar under den reserverade begäranden. Mer information finns i [Begär enheter](request-units.md).
3. **Design för mindre dokument för högre dataflöde**

    Begärandeavgiften (kostnaden för begäran bearbetning) för en viss åtgärd är direkt korrelerad till dokumentets storlek. Åtgärder på stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utformar ditt program för skalning och hög prestanda finns [i Partitionering och skalning i Azure Cosmos DB](partition-data.md).
