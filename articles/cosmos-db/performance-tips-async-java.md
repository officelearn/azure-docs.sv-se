---
title: Azure Cosmos DB-Prestandatips för Async Java | Microsoft Docs
description: Lär dig klienten konfigurationsalternativ för att förbättra prestanda för Azure Cosmos DB-databasen
keywords: hur vi kan förbättra databasens prestanda
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: sngun
ms.openlocfilehash: 233296a825653938da158fc70952c7fe7931498c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261833"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Prestandatips för Azure Cosmos DB och Async Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [NET](performance-tips.md)
> 

Azure Cosmos DB är en snabb och flexibel distribuerad databas som kan skalas sömlöst med garanterad svarstid och dataflöde. Du behöver inte göra ändringar i större arkitekturen eller skriva komplex kod för att skala din databas med Azure Cosmos DB. Skala upp och ned är lika enkelt som att göra en enda API-anrop eller SDK-anrop. Eftersom Azure Cosmos DB nås via nätverksanrop det finns dock klientsidan optimeringar som du kan göra för att uppnå högsta prestanda när du använder den [SQL Async Java SDK](sql-api-sdk-async-java.md).

Så om du begär ”hur kan jag förbättra min databasprestanda”? Överväg följande alternativ:

## <a name="networking"></a>Nätverk
   <a id="same-region"></a>
1. **Samordna klienter i samma Azure-region för prestanda**

    Om det är möjligt placera alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos DB-databasen. En ungefärlig jämförelse anrop till Azure Cosmos DB inom samma region som slutförs inom 1 – 2 ms, men fördröjningen mellan västra och östra västkust är > 50 ms. Den här fördröjningen kan förmodligen variera från begäran till begäran beroende på vilken väg begäran när det överförs från klienten till Azure-datacenter-gränsen. Lägsta möjliga tidsfördröjning uppnås genom att kontrollera att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB-slutpunkten. En lista över tillgängliga regioner finns i [Azure-regionerna](https://azure.microsoft.com/regions/#services).

    ![Bild av principen för Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-användning
1. **Installera den senaste SDK**

    Azure Cosmos DB SDK är ständigt bättre för att ge bästa möjliga prestanda. Se den [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) sidor för att fastställa den senaste SDK och granska förbättringar.
2. **Använda en singleton Azure Cosmos DB-klient under hela programmets livslängd**

    Varje AsyncDocumentClient-instans är trådsäker och utför effektiv anslutningshanteringen och cachelagring av adresser. Om du vill tillåta effektiv anslutningshanteringen och bättre prestanda genom att AsyncDocumentClient, rekommenderar vi att du använder en enda instans av AsyncDocumentClient per AppDomain för hela programmets livslängd.

   <a id="max-connection"></a>

3. **Justering ConnectionPolicy**

    Azure Cosmos DB begär görs över HTTPS/REST när du använder Async Java SDK och har utsatts för standard maximal storlek (1000). Det här värdet ska vara optimal för merparten av användningsfall. Men om du har en stor samling med många partitioner kan du kan ange den maximala storleken på administratörsanslutningspool till ett större antal (till exempel 1500) med hjälp av setMaxPoolSize.

4. **Justering parallella frågor partitionerade samlingar**

    Azure Cosmos DB SQL Async Java SDK har stöd för parallella frågor, som gör det möjligt att fråga en partitionerad samling parallellt (se [arbeta med SDK: erna](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) och de relaterade [kodexempel](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) för mer information). Parallella frågor är utformade för att förbättra svarstid och dataflöde över sin seriella motsvarighet.

    (a) ***justering setMaxDegreeOfParallelism\:***  parallella frågor arbete genom att fråga flera partitioner parallellt. Dock hämtas data från en enskild partitionerad samling seriellt med avseende på frågan. Så Använd setMaxDegreeOfParallelism kan ange hur många partitioner som har högsta risken för att uppnå de flesta högpresterande frågan, under förutsättning att alla andra system villkor är desamma. Om du inte vet hur många partitioner, du kan använda setMaxDegreeOfParallelism för att ange ett högre värde och systemet väljer minst (antal partitioner, tillhandahålls användarindata) som högsta grad av parallellitet. 

    Det är viktigt att notera att parallella frågor ger bästa fördelarna om data är jämnt fördelat över alla partitioner med avseende på frågan. Om partitionerade samlingen är partitionerad så att hela eller en merparten av de data som returneras av en fråga är koncentrerade i några partitioner (en partition i värsta fall) och sedan prestanda hos frågan skulle att skapa en flaskhals eftersom dessa partitioner.

    (b) ***justering setMaxBufferedItemCount\:***  parallell har utformats för att hämta förväg resultat när den aktuella batchen med resultat som behandlas av klienten. Den förhämtning hjälper i den övergripande svarstiden förbättring av en fråga. setMaxBufferedItemCount begränsar antalet förväg hämtade resultat. Ange setMaxBufferedItemCount till det förväntade antalet resultat som returneras (eller fler) gör att frågan för att få största möjliga nytta från förhämtning.

    Förhämtning fungerar på samma sätt oavsett MaxDegreeOfParallelism och det finns en enda buffert för data från alla partitioner.  

5. **Implementera backoff med getRetryAfterInMilliseconds intervall**

    Prestandatester, bör du öka belastningen tills en liten andel begäranden begränsas. Om prestandan för bör klientprogrammet backoff för server angiven återförsöksintervallet. Följer backoff säkerställer att du ägnar kortast möjliga tid att vänta mellan försöken. 
6. **Skala ut din klient-arbetsbelastning**

    Om du testar på hög genomströmning nivåer (> 50 000 RU/s), klientprogrammet kan bli en flaskhals på grund av den datorn tak som skall ut på CPU- eller användning. Om du når den här punkten kan fortsätta du att skicka ytterligare Azure Cosmos DB-kontot genom att skala ut dina klientprogram på flera servrar.

7. **Använda namn baserat adressering**

    Använda namnbaserat adressering, där länkar har formatet `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, i stället för SelfLinks (\_själv), som har formatet `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` att undvika hämtar ResourceIds över alla resurser som används för att konstruera länken. Dessutom som resurserna hämta återskapas (eventuellt med samma namn), kan cachelagring av dem inte hjälpa.

   <a id="tune-page-size"></a>
8. **Finjustera sidstorleken för frågor/läsning feeds för bättre prestanda**

    När du utför en massinläsning läsa dokument med hjälp av Läs feed-funktioner (till exempel readDocuments) eller när en SQL-fråga, returneras resultatet i ett segmenterade sätt om resultatet är för stor. Resultaten returneras i segment om 100 objekt eller 1 MB som standard, uppnås för beroende på vilken gräns först.

    För att minska antalet nätverk nätverksförfrågningar som krävs för att hämta alla tillämpliga resultat, kan du öka storleken sida med de [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) huvudet i begäran till upp till 1000. I fall där du vill visa endast några resultat, till exempel, om ditt användar-gränssnittet eller ett program-API returnerar endast 10 resulterar en tid, du kan också minska sidstorleken till 10 för att minska det dataflöde som används för läsningar och frågor.

    Du kan också ange sidstorleken med metoden setMaxItemCount.
    
9. **Använd lämplig Scheduler (Undvik att stjäla händelse loop-i/o Netty trådar)**

    Async Java SDK använder [netty](https://netty.io/) för icke-blockerande IO. SDK: N använder ett fast antal i/o netty händelse loop trådar (så många processorkärnor din dator har) för att köra i/o-åtgärder. Övervakas som returneras av API: et genererar resultat på en av de dela i/o händelse loop netty trådarna. Det är därför viktigt att inte blockera de dela i/o händelse loop netty trådarna. CPU arbetar eller blockerar åtgärden i i/o händelse loop netty tråden kan orsaka deadlock eller minska SDK dataflöde.

    Följande kod körs till exempel en cpu-intensiva arbetet på händelsen loopen netty i/o-tråd:

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

    När resultatet har tagits emot om du vill göra fungerar Processorintensiva på resultatet bör du undvika att göra osv händelse loop-i/o netty tråd. Du kan i stället ange en egen Scheduler för att ge dina egna tråd för att köra ditt arbete.

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

    Beroende på vilken typ av ditt arbete bör du använda lämpliga befintliga RxJava Scheduler för ditt arbete. Läs här [ ``Schedulers`` ](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Mer information finns i [Github-sidan](https://github.com/Azure/azure-cosmosdb-java) för Async Java SDK.

10. **Inaktivera loggning för netty's** Netty biblioteket loggning är trafikintensiva och måste vara avstängda (utelämna inloggning konfigurationen kanske inte är tillräckligt) att undvika ytterligare CPU-kostnader. Om du inte är i felsökningsläge loggar inaktivera netty helt och hållet. Om du använder log4j för att ta bort ytterligare CPU kostnader ``org.apache.log4j.Category.callAppenders()`` från netty att lägga till följande rad i din kodbas:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **OS öppna filer resursgräns** vissa Linux-system (till exempel Red Hat) har en övre gräns för hur många öppna filer och det totala antalet anslutningar. Kör följande om du vill visa aktuella begränsningar:

    ```bash
    ulimit -a
    ```

    Antal öppna filer (nofile) måste vara tillräckligt stor för att ha tillräckligt med utrymme för dina konfigurerade storlek på administratörsanslutningspool och andra filer med Operativsystemet. Du kan ändra för att tillåta en större storlek på administratörsanslutningspool.

    Öppna filen limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Lägg till/ändra följande rader:

    ```
    * - nofile 100000
    ```

12. **Använda interna SSL-implementering för netty** Netty kan använda OpenSSL direkt för SSL-implementering stack för att få bättre prestanda. Om den här konfigurationen netty tillbaka till Javas standard SSL-implementering.

    i Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    och Lägg till följande beroende till ditt projekt maven beroenden:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

För andra plattformar (Red Hat, Windows, Mac, osv.) som refererar till dessa instruktioner https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indexeringspolicy
 
1. **Undanta oanvända sökvägar från indexering för snabbare skrivningar**

    Indexeringsprincip för Azure Cosmos DB kan du ange vilka dokument sökvägar för att inkludera eller exkludera från indexering genom att använda indexering sökvägar (setIncludedPaths och setExcludedPaths). Användning av indexering sökvägar kan erbjuda bättre skrivprestanda och lagring med lägre index för scenarier där frågemönstren är kända i förväg, som korreleras indexering kostnader direkt till antal unika sökvägar som indexeras.  Till exempel visar följande kod hur du undantar en hela avsnittet dokument (alias) ett underträd) från indexering med den ”*” med jokertecken.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Mer information finns i [Azure Cosmos DB indexeringsprinciper](indexing-policies.md).

## <a name="throughput"></a>Dataflöde
<a id="measure-rus"></a>

1. **Mät och justering för lägre begäran begärandeenheter/sekund användning**

    Azure Cosmos DB erbjuder en omfattande uppsättning databasoperationer, inklusive Relations- och Hierarkifrågor med användardefinierade funktioner, lagrade procedurer och utlösare – allt opererar på dokumenten i en databassamling. Den kostnad som hör till var och en av dessa operationer varierar beroende på CPU, IO och minne som krävs för att slutföra åtgärden. I stället för att tänka på och hantera maskinvaruresurser kan tänka du på en begäransenhet (RU) som det enda måttet på de resurser som krävs för att utföra olika databasoperationer och behandla en programbegäran.

    Dataflöde etableras baserat på antalet [programbegäran](request-units.md) för varje behållare. Konsumtion för begäran om enheten utvärderas som en avgift per sekund. Program som överstiger den etablerade begäranhastigheten för sina behållare är begränsade tills frekvensen sjunker under den etablerade nivån för behållaren. Om programmet kräver högre dataflöde kan öka du dataflödet genom att etablera ytterligare enheter för programbegäran. 

    Komplexiteten för en fråga påverkar hur många enheter för programbegäran som förbrukas för en åtgärd. Antal predikat, natur predikat, antal UDF: er och storleken på källan datauppsättningen alla påverkar kostnaden för frågeåtgärder.

    Att mäta arbetet med att alla åtgärder (skapa, uppdatera eller ta bort), granska den [x-ms-begäran-avgift](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) huvudet för att mäta antalet enheter för programbegäran som förbrukas av de här åtgärderna. Du kan också titta på motsvarande RequestCharge-egenskapen i ResourceResponse<T> eller FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```             

    Kostnad för begäran som returnerades i det här sidhuvudet är en del av det tilldelade dataflödet. Till exempel om du har 2000 etablerade RU/s och om den föregående frågan returnerar 1000 1KB-dokument, kostnaden för åtgärden är 1 000. Inom en sekund godkänner servern därför bara två sådana begäranden innan hastighet som begränsar efterföljande förfrågningar. Mer information finns i [programbegäran](request-units.md) och [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Hantera hastighet begränsar/begärandehastighet för stor**

    När en klient försöker överskrida reserverat dataflöde för ett konto, finns det inga prestandaförsämring på servern och ingen användning av dataflödeskapacitet utöver nivån som är reserverade. Servern kommer sluta på begäran med RequestRateTooLarge (HTTP-statuskod 429) och returnera den [x-ms-återförsök-efter-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) huvud som anger hur lång tid i millisekunder som användaren måste vänta innan ett nytt försök begäran.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK: erna alla implicit fånga upp svaret, respekterar det server angiven sidhuvudet retry-after och försök begäran. Om inte ditt konto är samtidigt som används av flera klienter, lyckas nästa återförsök.

    Om du har mer än en för klienten kumulativt konsekvent ovan blir förfrågningsfrekvensen Standardantal försök som för närvarande inställd på 9 internt av klienten inte finns tillräckligt; i det här fallet genererar klienten ett DocumentClientException med statuskoden 429 till programmet. Standardvärdet för antal återförsök kan ändras genom att använda setRetryOptions på ConnectionPolicy-instans. Som standard returneras DocumentClientException med statuskoden 429 efter en kumulativ väntetid på 30 sekunder om begäran fortsätter att fungera ovan blir förfrågningsfrekvensen. Detta inträffar även när det aktuella antalet återförsök är mindre än antalet försök, oavsett om det är standardvärdet 9 eller ett användardefinierat värde.

    När det automatiska återförsöksbeteendet hjälper oss för att förbättra återhämtning och användbarhet för de flesta program, kan det ha följt emot varandra när du gör prestandamått, särskilt när mäta svarstiden.  Svarstid för klient-observerade att utnyttja om experimentet som kommer till server-begränsning och leder till att klienten SDK att göra om tyst. Mät den kostnad som returneras av varje åtgärd för att undvika svarstidsspikar under prestanda experiment, och se till att begäranden fungerar nedan reserverade begäranhastigheten. Mer information finns i [programbegäran](request-units.md).
3. **Design för mindre dokument för högre dataflöde**

    Kostnaden för begäran (kostnaden för bearbetning av begäran) för en viss åtgärd korreleras direkt till storleken på dokumentet. Åtgärder för stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utformar ditt program för skalning och hög prestanda, finns i [partitionering och skalning i Azure Cosmos DB](partition-data.md).
