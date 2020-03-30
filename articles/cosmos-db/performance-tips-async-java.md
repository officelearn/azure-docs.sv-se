---
title: Prestandatips för Azure Cosmos DB för Async Java
description: Lär dig alternativ för klientkonfiguration för att förbättra Azure Cosmos-databasens prestanda
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 89df941eb6ebaad6e078c278f1ed883db5528c7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152573"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Prestandatips för Azure Cosmos DB och Async Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB är en snabb och flexibel distribuerad databas som skalas sömlöst med garanterad svarstid och dataflöde. Du behöver inte göra större arkitekturändringar eller skriva komplex kod för att skala databasen med Azure Cosmos DB. Skala upp och ned är lika enkelt som att göra ett enda API-anrop eller SDK-metodanrop. Men eftersom Azure Cosmos DB nås via nätverksamtal finns det optimering på klientsidan som du kan göra för att uppnå toppprestanda när du använder [SQL Async Java SDK](sql-api-sdk-async-java.md).

Så om du frågar "Hur kan jag förbättra min databas prestanda?" överväga följande alternativ:

## <a name="networking"></a>Nätverk

* **Anslutningsläge: Använd direktläge**
<a id="direct-connection"></a>
    
    Hur en klient ansluter till Azure Cosmos DB har viktiga konsekvenser för prestanda, särskilt när det gäller svarstid på klientsidan. *ConnectionMode* är en nyckelkonfigurationsinställning som är tillgänglig för att konfigurera klienten *ConnectionPolicy*. För Async Java SDK är de två tillgängliga ConnectionModes:  
      
    * [Gateway (standard)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Direkt](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    Gateway-läge stöds på alla SDK-plattformar och det är det konfigurerade alternativet som standard. Om dina program körs i ett företagsnätverk med strikta brandväggsbegränsningar är gateway-läge det bästa valet eftersom det använder standard-HTTPS-porten och en enda slutpunkt. Prestanda kompromissen är dock att Gateway-läge innebär ytterligare ett nätverkshopp varje gång data läss eller skrivs till Azure Cosmos DB. På grund av detta ger direktläget bättre prestanda på grund av färre nätverkshopp.

    *ConnectionMode* konfigureras under konstruktionen av *DocumentClient-instansen* med parametern *ConnectionPolicy.*
    
    ```java
        public ConnectionPolicy getConnectionPolicy() {
          ConnectionPolicy policy = new ConnectionPolicy();
          policy.setConnectionMode(ConnectionMode.Direct);
          policy.setMaxPoolSize(1000);
          return policy;
        }

        ConnectionPolicy connectionPolicy = new ConnectionPolicy();
        DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

* **Samlokaliseringsklienter i samma Azure-region för prestanda**<a id="same-region"></a>

    När det är möjligt placerar du alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos-databasen. För en ungefärlig jämförelse, anrop till Azure Cosmos DB inom samma region slutföras inom 1-2 ms, men svarstiden mellan väst- och östkusten i USA är >50 ms. Den här svarstiden kan troligen variera från begäran till begäran beroende på vilken väg som hämtas av begäran när den skickas från klienten till Azure-datacentergränsen. Lägsta möjliga svarstid uppnås genom att se till att anropandet finns inom samma Azure-region som den etablerade Azure Cosmos DB-slutpunkten. En lista över tillgängliga regioner finns i [Azure Regions](https://azure.microsoft.com/regions/#services).

    ![Illustration av Azure Cosmos DB-anslutningsprincipen](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK-användning
* **Installera den senaste SDK**

    Azure Cosmos DB SDK:er förbättras ständigt för att ge bästa prestanda. Se [Azure Cosmos DB SDK-sidor](sql-api-sdk-async-java.md) för att fastställa de senaste SDK- och granskningsförbättringarna.

* **Använda en Azure Cosmos DB-klient för singleton-azure cosmos för ditt programs livstid**

    Varje AsyncDocumentClient-instans är trådsäker och utför effektiv anslutningshantering och adresscachelagring. För att möjliggöra effektiv anslutningshantering och bättre prestanda av AsyncDocumentClient, rekommenderas att använda en enda instans av AsyncDocumentClient per AppDomain för programmets livstid.

   <a id="max-connection"></a>

* **Justera ConnectionPolicy**

    Som standard görs Cosmos DB-begäranden i direktläge via TCP när Async Java SDK.Default, Direct mode Cosmos DB requests are made over TCP when using the Async Java SDK. Internt använder SDK en speciell direct-lägesarkitektur för att dynamiskt hantera nätverksresurser och få bästa prestanda.

    I Async Java SDK är direktläge det bästa valet för att förbättra databasprestanda med de flesta arbetsbelastningar. 

    * ***Översikt över direktläge***

        ![Illustration av direct-lägesarkitekturen](./media/performance-tips-async-java/rntbdtransportclient.png)

        Arkitekturen på klientsidan som används i direktläge möjliggör förutsägbar nätverksanvändning och multiplexåtkomst till Azure Cosmos DB-repliker. Diagrammet ovan visar hur direktläge dirigerar klientbegäranden till repliker i Cosmos DB-backend. Arkitekturen Direktläge allokerar upp till 10 **kanaler** på klientsidan per DB-replik. En kanal är en TCP-anslutning som föregås av en begäran buffert, vilket är 30 begäranden djupt. Kanalerna som tillhör en replik allokeras dynamiskt efter behov av replikens **tjänstslutpunkt**. När användaren utfärdar en begäran i direktläge dirigerar **TransportClient** begäran till rätt tjänstslutpunkt baserat på partitionsnyckeln. **Begärandekön** buffrar begäranden före tjänstens slutpunkt.

    * ***Konfigurationsalternativ för ConnectionPolicy för direktläge***

        Som ett första steg använder du följande rekommenderade konfigurationsinställningar nedan. Kontakta [Azure Cosmos DB-teamet](mailto:CosmosDBPerformanceSupport@service.microsoft.com) om du stöter på problem med just det här avsnittet.

        Om du använder Azure Cosmos DB som referensdatabas (det vill säga databasen används för många punktläsningsåtgärder och få skrivåtgärder), kan det vara acceptabelt att ange *inaktivEndpointTimeout* till 0 (det vill säga ingen timeout).


        | Konfigurationsalternativ       | Default    |
        | :------------------:       | :-----:    |
        | bufferPageSize             | 8192       |
        | anslutningTimeout          | "PT1M"     |
        | idleChannelTimeout         | "PT0S"     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity (maxBufferCapacity)          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | ta emotHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | "PT5S"     |
        | requestTimeout             | "PT1M"     |
        | begäranTidaResolution     | "PT0.5S"   |
        | sendHangDetectionTime      | "PT10S"    |
        | shutdownTimeout            | "PT15S"    |

    * ***Programmeringstips för direktläge***

        Granska azure Cosmos DB [Async Java SDK Felsökningsartikel](troubleshoot-java-async-sdk.md) som en baslinje för att lösa eventuella Async Java SDK-problem.

        Några viktiga programmeringstips när du använder Direktläge:

        + **Använd flertrådning i ditt program för effektiv TCP-dataöverföring** - När du har gjort en begäran bör ditt program prenumerera för att ta emot data på en annan tråd. Om du inte gör det tvingar du oavsiktlig "halv duplex"-åtgärd och de efterföljande begärandena blockeras i väntan på den tidigare begärans svar.

        + **Utför beräkningsintensiva arbetsbelastningar på en dedikerad tråd** - Av liknande orsaker till föregående tips är åtgärder som komplex databehandling bäst placerade i en separat tråd. En begäran som hämtar data från ett annat datalager (till exempel om tråden använder Azure Cosmos DB- och Spark-datalager samtidigt) kan uppleva ökad latens och det rekommenderas att skapa ytterligare en tråd som väntar på ett svar från den andra datalager.

            + Det underliggande nätverks-IO i Async Java SDK hanteras av Netty, se dessa [tips för att undvika kodningsmönster som blockerar Netty IO trådar](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).

        + **Datamodellering** – Azure Cosmos DB SLA förutsätter att dokumentstorleken är mindre än 1KB. Optimera din datamodell och programmering för att gynna mindre dokumentstorlek leder i allmänhet till minskad latens. Om du behöver lagring och hämtning av dokument som är större än 1KB, är den rekommenderade metoden för dokument som länkar till data i Azure Blob Storage.


* **Justera parallella frågor för partitionerade samlingar**

    Azure Cosmos DB SQL Async Java SDK stöder parallella frågor, vilket gör att du kan fråga en partitionerad samling parallellt. Mer information finns i [kodexempel](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) relaterade till att arbeta med SDK:erna. Parallella frågor är utformade för att förbättra frågefördröjning och dataflöde över deras seriella motsvarighet.

    * ***Tuning setMaxDegreeOfParallelism\:***
    
        Parallella frågor fungerar genom att fråga flera partitioner parallellt. Data från en enskild partitionerad samling hämtas dock seriellt med avseende på frågan. Så, använd setMaxDegreeOfParallelism för att ställa in antalet partitioner som har maximal chans att uppnå den mest högpresterande frågan, förutsatt att alla andra systemvillkor förblir desamma. Om du inte vet antalet partitioner kan du använda setMaxDegreeOfParallelism för att ställa in ett högt antal, och systemet väljer det minsta (antal partitioner, användarindata) som maximal grad av parallellism.

        Det är viktigt att notera att parallella frågor ger de bästa fördelarna om data är jämnt fördelade över alla partitioner med avseende på frågan. Om den partitionerade samlingen är partitionerad på ett sådant sätt att alla eller en majoritet av de data som returneras av en fråga är koncentrerad till några partitioner (en partition i värsta fall), då skulle frågans prestanda flaskhalsas av dessa partitioner.

    * ***JusteringsuppsättningMaxBufferedItemCount\:***
    
        Parallellfråga är utformad för att förhämtningsresultat medan den aktuella batchen med resultat bearbetas av klienten. Förhämtningen hjälper till att förbättra en fråga totalt sett. setMaxBufferedItemCount begränsar antalet förhämtningsresultat. Om du ställer in setMaxBufferedItemCount till det förväntade antalet returnerade resultat (eller ett högre tal) kan frågan få maximal nytta av förhämtning.

        Förhämtning fungerar på samma sätt oavsett MaxDegreeOfParallelism, och det finns en enda buffert för data från alla partitioner.

* **Implementera backoff vid intervallen getRetryAfterInMilliseconds**

    Under prestandatestning bör du öka belastningen tills en liten mängd begäranden begränsas. Om det begränsas ska klientprogrammet backas av för det serverspecificerade återförsöksintervallet. Med respekt för backoff säkerställer att du tillbringar minimal tid att vänta mellan försök.

* **Skala ut din klient-arbetsbelastning**

    Om du testar på höga dataflödesnivåer (>50 000 RU/s) kan klientprogrammet bli flaskhalsen på grund av att maskinen begränsar cpu- eller nätverksanvändningen. Om du når den här punkten kan du fortsätta att driva Azure Cosmos DB-kontot vidare genom att skala ut dina klientprogram över flera servrar.

* **Använd namnbaserad adressering**

    Använd namnbaserad adressering, där länkar `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`har formatet ,\_i stället för `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` SelfLinks ( self), som har formatet för att undvika att hämta ResourceIds av alla resurser som används för att konstruera länken. Eftersom dessa resurser återskapas (eventuellt med samma namn) kan det hända att cachelagring inte hjälper.

   <a id="tune-page-size"></a>

* **Justera sidstorleken för frågor/läsflöden för bättre prestanda**

    När du läser en massläsning av dokument med hjälp av läsflödesfunktioner (till exempel readDocuments) eller när du utfärdar en SQL-fråga returneras resultaten på ett segmenterat sätt om resultatuppsättningen är för stor. Som standard returneras resultaten i segment med 100 objekt eller 1 MB, beroende på vilken gräns som uppnås först.

    Om du vill minska antalet nätverksrundningsturer som krävs för att hämta alla tillämpliga resultat kan du öka sidstorleken med hjälp av begäranshuvudet för [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) till upp till 1 000. I de fall där du behöver visa endast ett fåtal resultat, till exempel om ditt användargränssnitt eller program-API returnerar endast 10 resultat per gång, kan du också minska sidstorleken till 10 för att minska dataflödet som förbrukas för läsningar och frågor.

    Du kan också ange sidstorlek med metoden setMaxItemCount.

* **Använd lämplig schemaläggare (Undvik att stjäla Event loop IO Netty trådar)**

    Async Java SDK använder [netty](https://netty.io/) för icke-blockerande IO. SDK använder ett fast antal IO netty händelse loop trådar (så många CPU-kärnor din dator har) för att köra IO-åtgärder. Det Observerable som returneras av API avger resultatet på en av de delade IO-händelselooptrådarna netty. Så det är viktigt att inte blockera den delade IO händelse loop netty trådar. Att utföra CPU-intensivt arbete eller blockering på IO-händelseloopen netty-tråden kan orsaka dödläge eller avsevärt minska SDK-dataflödet.

    Till exempel kör följande kod ett processorintensivt arbete på händelseloopens IO netty-tråd:

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Efter resultatet tas emot om du vill göra CPU-intensivt arbete på resultatet bör du undvika att göra det på händelse loop IO netty tråd. Du kan istället ange din egen Scheduler för att ge din egen tråd för att köra ditt arbete.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    Baserat på vilken typ av arbete du bör använda lämplig befintlig RxJava Scheduler för ditt arbete. Läs [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)här .

    Mer information finns på [GitHub-sidan](https://github.com/Azure/azure-cosmosdb-java) för Async Java SDK.

* **Inaktivera nettys loggning**

    Netty-biblioteksloggning är pratsam och måste inaktiveras (det kanske inte räcker med att undertrycka inloggningen) för att undvika ytterligare CPU-kostnader. Om du inte är i felsökningsläge inaktiverar du nettys loggning helt och hållet. Så om du använder log4j för att ta ``org.apache.log4j.Category.callAppenders()`` bort de extra CPU-kostnader som uppstår från från netty lägga till följande rad till din kodbas:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Os Öppna filer Resursgräns**
 
    Vissa Linux-system (som Red Hat) har en övre gräns för antalet öppna filer och så det totala antalet anslutningar. Kör följande om du vill visa de aktuella gränserna:

    ```bash
    ulimit -a
    ```

    Antalet öppna filer (nofile) måste vara tillräckligt stort för att ha tillräckligt med utrymme för din konfigurerade anslutningspoolstorlek och andra öppna filer av operativsystemet. Den kan ändras för att möjliggöra en större anslutningspoolstorlek.

    Öppna filen limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Lägg till/ändra följande rader:

    ```
    * - nofile 100000
    ```

* **Använd inbyggd SSL-implementering för netty**

    Netty kan använda OpenSSL direkt för SSL-implementeringsstack för att uppnå bättre prestanda. I avsaknad av denna konfiguration netty kommer att falla tillbaka till Javas standard SSL-implementering.

    på Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    och lägg till följande beroende i projektets maven-beroenden:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

För andra plattformar (Red Hat, Windows, Mac, etc.) finns i dessa instruktionerhttps://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indexeringsprincip
 
* **Utesluta sökvägar som inte används från indexering för att få snabbare skrivning**

    Azure Cosmos DB:s indexeringsprincip gör att du kan ange vilka dokumentsökvägar som ska inkluderas eller uteslutas från indexering genom att utnyttja indexeringsvägar (setIncludedPaths och setExcludedPaths). Användningen av indexeringsvägar kan erbjuda förbättrad skrivprestanda och lägre indexlagring för scenarier där frågemönstren är kända i förväg, eftersom indexeringskostnaderna är direkt korrelerade till antalet unika sökvägar som indexeras. Följande kod visar till exempel hur du utesluter ett helt avsnitt i dokumenten (kallas även underträd) från att indexera med jokertecknet "*".

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

* **Mäta och justera för lägre begäranheter/andra användning**

    Azure Cosmos DB erbjuder en omfattande uppsättning databasåtgärder, inklusive relations- och hierarkiska frågor med UDF:er, lagrade procedurer och utlösare – alla som arbetar med dokumenten i en databassamling. Den kostnad som hör till var och en av dessa operationer varierar baserat på vilken CPU, vilka IO-resurser och hur mycket minne som krävs för att slutföra operationen. I stället för att tänka på och hantera maskinvaruresurser kan du tänka dig en begäranhet (RU) som ett enda mått för de resurser som krävs för att utföra olika databasåtgärder och serva en programbegäran.

    Dataflöde etableras baserat på antalet [begärandeenheter](request-units.md) som angetts för varje behållare. Förbrukning av begäranden enhet utvärderas som en hastighet per sekund. Program som överskrider den etablerade enhetssatsen för begäran för sin behållare är begränsade tills hastigheten sjunker under den etablerade nivån för behållaren. Om ditt program kräver en högre nivå av dataflöde kan du öka dataflödet genom att etablera ytterligare begäranheter.

    Komplexiteten i en fråga påverkar hur många begärandeenheter som förbrukas för en åtgärd. Antalet predikat, typ av predikat, antal UDF:er och storleken på källdatauppsättningen påverkar alla kostnaden för frågeåtgärder.

    Om du vill mäta omkostnaderna för en åtgärd (skapa, uppdatera eller ta bort) inspekterar [du x-ms-begäran-avgiftshuvudet](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) för att mäta antalet begärandeenheter som förbrukas av dessa åtgärder. Du kan också titta på motsvarande RequestCharge-egenskap i ResourceResponse\<\<T> eller FeedResponse T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Den begärandeavgift som returneras i det här huvudet är en bråkdel av det etablerade dataflödet. Om du till exempel har etablerat 2000 RU/s och om föregående fråga returnerar 1000 1KB-dokument, är kostnaden för operationen 1000. Som sådan, inom en sekund, servern respekterar endast två sådana förfrågningar innan kurs begränsa efterföljande begäranden. Mer information finns i [Begär enheter](request-units.md) och [räknaren för begäranhet](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Hantera hastighetsbegränsning/begäranden för stor**

    När en klient försöker överskrida det reserverade dataflödet för ett konto, finns det ingen prestandaförsämring på servern och ingen användning av dataflödeskapacitet utöver den reserverade nivån. Servern avslutar begäran i förebyggande syfte med RequestRateTooLarge (HTTP-statuskod 429) och returnerar [x-ms-retry-after-ms-huvudet](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) som anger hur lång tid, i millisekunder, som användaren måste vänta innan begäran upprepas.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Alla SDK:er fångar underförstått det här svaret, respekterar det serverinderade återförsöks-efter-huvudet och försöker igen med begäran. Om inte ditt konto används samtidigt av flera klienter, kommer nästa återförsök att lyckas.

    Om du har mer än en klient som är kumulativt verksamt över begäranden, kanske det för närvarande inte räcker med att antalet standardförsök är 9 internt av klienten. I det här fallet genererar klienten en DocumentClientException med statuskod 429 till programmet. Standardantalet för återförsök kan ändras med hjälp av setRetryOptions i ConnectionPolicy-instansen. Som standard returneras DocumentClientException med statuskod 429 efter en sammanlagd väntetid på 30 sekunder om begäran fortsätter att fungera över begäranden. Detta inträffar även när det aktuella antalet försök på nytt är mindre än antalet försök per max, oavsett om det är standardvärdet 9 eller ett användardefinierat värde.

    Även om det automatiserade återförsöksbeteendet bidrar till att förbättra återhämtningen och användbarheten för de flesta program, kan det gå i strid när prestandariktmärkena ändras, särskilt när svarstiden mäts. Den klient observerade svarstiden kommer att öka om experimentet träffar serverbegränsningen och gör att klienten SDK tyst försöker igen. För att undvika fördröjningsstopp under prestandaexperiment mäter du debiteringen som returneras av varje åtgärd och säkerställer att begäranden fungerar under den reserverade begäranden. Mer information finns i [Begär enheter](request-units.md).

* **Design för mindre dokument för högre dataflöde**

    Begärandeavgiften (kostnaden för begäran bearbetning) för en viss åtgärd är direkt korrelerad till dokumentets storlek. Åtgärder på stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utformar ditt program för skalning och hög prestanda finns [i Partitionering och skalning i Azure Cosmos DB](partition-data.md).
