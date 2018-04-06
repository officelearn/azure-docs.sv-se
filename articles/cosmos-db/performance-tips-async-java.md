---
title: Azure DB Cosmos Prestandatips för asynkrona Java | Microsoft Docs
description: Läs klienten konfigurationsalternativ för att förbättra Azure Cosmos DB databasprestanda
keywords: hur du förbättrar databasens prestanda
services: cosmos-db
author: mimig1
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: dfe8f426-3c98-4edc-8094-092d41f2795e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mimig
ms.openlocfilehash: 88d9859ade8f2cd3c5f11dffa8e754e83fc8b4d4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Prestandatips för Azure Cosmos DB och asynkrona Java
Azure Cosmos-DB är en snabb och flexibel distribuerad databas som kan skalas sömlöst med garanterad svarstid och genomströmning. Du behöver inte göra ändringar av större arkitektur eller skriva komplex kod för att skala din databas med Azure Cosmos DB. Skala upp och ner är lika enkelt som att göra en enda API-anrop eller SDK-anrop. Men eftersom Azure Cosmos DB går att nå via nätverket anrop finns på klientsidan optimeringar kan du uppnå högsta prestanda när du använder den [SQL asynkrona Java SDK](sql-api-sdk-async-java.md).

Så om du begär ”hur kan jag förbättra Mina databasprestanda”? Överväg följande alternativ:

## <a name="networking"></a>Nätverk
   <a id="same-region"></a>
1. **Samordna klienter i samma Azure-region för prestanda**

    När det är möjligt placera alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos-DB-databas. Anrop till Azure Cosmos DB inom samma region som utför inom 1 – 2 ms för en ungefärlig jämförelse, men fördröjning mellan Väst och av USA: S > 50 ms. Den här fördröjningen kan förmodligen variera från begäran till begäran beroende på vägen som begäran när det överförs från klienten till den Azure datacenter-gränsen. Lägsta möjliga tidsfördröjning uppnås genom att säkerställa att det anropande programmet finns i samma Azure-region som etablerade Azure DB som Cosmos-slutpunkt. En lista över tillgängliga regioner, se [Azure-regioner](https://azure.microsoft.com/regions/#services).

    ![Bild av principen för Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-användning
1. **Installera den senaste SDK**

    SDK: er för Azure Cosmos DB är ständigt bättre för att tillhandahålla bästa prestanda. Finns det [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) sidor för att avgöra den senaste SDK och granska förbättringar.
2. **Använd klienten en singleton-Azure Cosmos DB för livslängden för ditt program**

    Varje AsyncDocumentClient-instansen är trådsäker och utför effektiv hantering och cachelagring av adresser. Om du vill tillåta effektiv hantering och bättre prestanda genom att AsyncDocumentClient rekommenderas att använda en enda instans av AsyncDocumentClient per AppDomain för livslängden för programmet.

   <a id="max-connection"></a>

3. **Justera ConnectionPolicy**

    Azure Cosmos-DB-begäranden görs över HTTPS/RESTEN när du använder Async Java SDK och är föremål för de standard max storleken på administratörsanslutningspool (1000). Det här värdet ska vara perfekt för flesta användningsområden. Om du har en mycket stor samling med många partitioner du kan dock ange den maximala storleken på administratörsanslutningspool till ett större antal (t.ex., 1500) med hjälp av setMaxPoolSize.

4. **Justera parallella frågor för partitionerade samlingar**

    Azure Cosmos DB SQL asynkrona Java SDK stöder parallella frågor som gör det möjligt att fråga en partitionerad samling parallellt (se [arbeta med SDK: erna](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) och den relaterade [kodexempel](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) för mer information). Parallella frågor är utformade för att förbättra svarstid och genomströmning över sin seriella motsvarighet.

    (a) ***justera setMaxDegreeOfParallelism\:***  parallella frågor arbete genom att fråga flera partitioner parallellt. Dock hämtas data från en enskild partitionerad samling seriellt med avseende på frågan. Så Använd setMaxDegreeOfParallelism för att ange antalet partitioner som har högsta risken för att uppnå de flesta performant frågan, under förutsättning att andra system villkor desamma. Om du inte vet antalet partitioner, du kan använda setMaxDegreeOfParallelism för att ange ett högre värde och minsta (antal partitioner, tillhandahålls användarindata) väljs automatiskt som högsta grad av parallellitet. 

    Det är viktigt att notera att parallella frågor ger bästa fördelarna om data är jämnt fördelad över alla partitioner med avseende på frågan. Om partitionerade samlingen är partitionerad så att hela eller en majoritet av data som returneras av en fråga samlas i några partitioner (en partition i värsta fall) och sedan dessa partitioner skulle flaskhals prestanda för frågan.

    (b) ***justera setMaxBufferedItemCount\:***  Parallel query är utformat för att före hämtar resultat när den aktuella batchen med resultat som behandlas av klienten. Det hjälper till att hämtas först i den övergripande svarstiden förbättring av en fråga. setMaxBufferedItemCount begränsar antalet före hämtade resultat. Anger setMaxBufferedItemCount till det förväntade antalet resultat som returneras (eller fler) kan frågan för att hämta det maximala fördelar.

    Hämta fungerar på samma sätt oavsett MaxDegreeOfParallelism och det finns en enda buffert för data från alla partitioner.  

5. **Implementera backoff med getRetryAfterInMilliseconds intervall**

    Under prestandatester, bör du öka belastningen tills en liten andel begäranden hämta begränsas. Om begränsas, bör klientprogrammet backoff för intervallet-server har angetts. Respektera backoff garanterar att du ägnar minimal mängd väntetid mellan försöken. Mer information finns i [Exceeding reserverat dataflöde gränser](request-units.md#RequestRateTooLarge) och DocumentClientException.getRetryAfterInMilliseconds.
6. **Skala upp din klient arbetsbelastning**

    Om du testar på hög genomströmning nivåer (> 50 000 RU/s), klientprogrammet kan bli en flaskhals på grund av den datorn tak som skall ut på processor eller användning. Om du når den här punkten kan kan du fortsätta att skicka ytterligare Azure Cosmos DB kontot genom att skala ut ditt klientprogram på flera servrar.

7. **Använd namn baserat adressering**

    Använd namnbaserat adressering, där länkar har formatet `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, i stället för SelfLinks (\_self), som har formatet `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` att undvika att hämta ResourceIds för alla resurser som används för att skapa länken. Dessutom som resurserna hämta återskapas (eventuellt med samma namn) kan cachelagring av dem inte hjälpa.

   <a id="tune-page-size"></a>
8. **Finjustera sidstorleken för frågor/läsa feeds för bättre prestanda**

    När utför en grupp av dokument med hjälp av Läs feed funktioner (till exempel readDocuments) eller läsas vid utfärdande av en SQL-fråga, returneras resultatet i ett segmenterade sätt om resultatet är för stor. Resultaten returneras i mängder 100 objekt eller 1 MB som standard, oavsett vilken gränsen är träffar första.

    För att minska antalet nätverket förfrågningar krävs för att hämta alla tillämpliga resultat, kan du öka sidan storlek med den [x-ms-max--antal objekt](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) huvudet i begäran till upp till 1000. I fall där du vill visa endast några resultat, till exempel om ditt användar-gränssnittet eller programmet API returnerar bara 10 resulterar en tid, du kan också minska sidstorleken till 10 för att minska den används för läsning och frågor.

    Du kan också ange sidstorlek med metoden setMaxItemCount.
    
9. **Använd lämplig Schemaläggaren (Undvik att stjäla Eventloop IO Netty trådar)**

    Asynkrona Java SDK använder [netty](https://netty.io/) för icke-blockerande IO. SDK använder ett fast antal i/o netty eventloop trådar (valfritt antal processorkärnor din dator har) för att köra i/o-åtgärder. Observeras som returneras av API skickar resultatet på en delad IO eventloop netty trådar. Det är därför viktigt att inte blockera delade IO eventloop netty trådar. Processorn arbetar kan blockerar åtgärden i i/o-eventloop netty tråden orsaka deadlock eller avsevärt minska SDK.

    Följande kod kör till exempel en cpu arbetar på eventloop-i/o netty tråd:

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

    När resultatet har tagits emot om du vill göra fungerar Processorintensiva på resultatet bör du undvika att göra så vidare eventloop IO netty tråd. I stället kan du ange egna Schemaläggaren för att tillhandahålla egna tråd för att köra ditt arbete.

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

    Beroende på vilken typ av ditt arbete bör du använda lämpliga befintliga RxJava Schemaläggaren för ditt arbete. Läsa här [ ``Schedulers`` ](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Mer information finns i den [Github-sidan](https://github.com/Azure/azure-cosmosdb-java) för asynkrona Java SDK.

10. **Inaktivera netty's loggning** Netty biblioteket loggning chatty och måste vara avstängda (utelämna logg i konfigurationen kanske inte är tillräckligt) att undvika ytterligare CPU-kostnader. Om du inte är i felsökningsläge loggningen inaktivera netty helt. Om du använder log4j för att ta bort de extra CPU-kostnaderna genom ``org.apache.log4j.Category.callAppenders()`` från netty att lägga till följande rad i din kodbas:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **OS öppna filer resursgräns** vissa Linux-system (till exempel Redhat) har en övre gräns för antalet öppna filer, och det totala antalet anslutningar. Kör följande om du vill visa aktuella begränsningar:

    ```bash
    ulimit -a
    ```

    Antal öppna filer (nofile) måste vara tillräckligt stor för att ha tillräckligt med utrymme för dina konfigurerade storlek på administratörsanslutningspool och andra filer av Operativsystemet. Den kan ändras om du vill tillåta en större storlek på administratörsanslutningspool.

    Öppna filen limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Lägga till och ändra följande rader:

    ```
    * - nofile 100000
    ```

12. **Använd ursprunglig SSL-implementering för netty** Netty kan använda OpenSSL direkt för SSL-implementering stack för att få bättre prestanda. Om detta inte konfigurationen netty kommer att gå Javas standard SSL-implementering.

    på Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    och Lägg till följande beroende i dina projekt maven beroenden:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Referera till dessa instruktioner för andra plattformar (Redhat, Windows, Mac, osv.) https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indexeringspolicy
 
1. **Uteslut oanvända sökvägar från indexering för snabbare skrivningar**

    Azure Cosmos DB indexprincip kan du ange vilka sökvägar dokument om du vill inkludera eller exkludera från indexering genom att använda indexering sökvägar (setIncludedPaths och setExcludedPaths). Användning av indexering sökvägar kan erbjuda bättre skrivprestanda och lägre index lagring för scenarier där frågemönster är kända i förväg, som indexerings kostnader direkt kopplas ihop med antalet unika sökvägar som indexeras.  Följande kod visar exempelvis undanta (kallas även ett helt avsnitt av dokumenten en underkatalog) från att använda indexering av ”*” jokertecken.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Mer information finns i [Azure Cosmos DB indexering principer](indexing-policies.md).

## <a name="throughput"></a>Dataflöde
<a id="measure-rus"></a>

1. **Mäter och finjustera för lägre begäran enheter per sekund användning**

    Azure Cosmos-DB erbjuder en omfattande uppsättning databasåtgärder inklusive relationella och hierarkiska frågor med UDF: er, lagrade procedurer och utlösare – alla operativsystem i dokumenten inom en samling i databasen. Kostnaden för att var och en av dessa åtgärder varierar beroende på CPU, IO och minne som krävs för att slutföra åtgärden. I stället för att tänka på och hantera maskinvaruresurser, Tänk på begäran-enhet (RU) som en enda åtgärd för de resurser som krävs för att utföra olika databasåtgärder och ett program begärde-tjänsten.

    Genomströmning etableras baserat på antalet [programbegäran](request-units.md) för varje behållare. Konsumtion av begäran enheten utvärderas som en sats per sekund. Program som överskrider etablerade begärandehastighet enhet för deras behållare är begränsade förrän frekvensen sjunker under nivån etablerade för behållaren. Om programmet kräver en högre säkerhetsnivå för genomflöde, kan du öka dina genomflödet genom att etablera ytterligare frågeenheter. 

    Komplexiteten i en fråga påverkar hur många enheter som begäran används för en åtgärd. Antalet predikat, predikat, antalet UDF: er och storleken på alla källa datauppsättningen påverkar kostnaden för frågor.

    För mätning av alla åtgärder (skapa, uppdatera eller ta bort) inspektera den [x-ms-begäran-kostnad](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) huvudet för att mäta antalet frågeenheter som används av dessa åtgärder. Du kan också titta på den motsvarande RequestCharge-egenskapen i ResourceResponse<T> eller FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```             

    Begäran-tillägget som returneras i det här sidhuvudet är en del av din dataflöde. Till exempel om du har 2000 RU/s etablerats och om den föregående frågan returnerar 1000 1KB-dokument, kostnaden för åtgärden är 1000. Inom en sekund godkänner servern därför bara två sådana begäranden innan begränsning efterföljande förfrågningar. Mer information finns i [programbegäran](request-units.md) och [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Hantera hastighet begränsa/förfrågningar för stor**

    När en klient försöker överskrida reserverat dataflöde för ett konto, finns det inga prestandaförsämring på servern och ingen användning av genomflödeskapaciteten utanför den reserverade. Servern förebyggande syfte kan avsluta begäran och RequestRateTooLarge (HTTP-statuskod 429) och returnera den [x-ms-retry-efter-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) huvud som anger hur lång tid i millisekunder som användaren måste vänta innan ett nytt försök begäran.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK: erna alla implicit catch-svaret respektera server angetts försök igen efter rubriken och försöka. Om ditt konto används samtidigt av flera klienter, lyckas nästa försök.

    Om du har mer än ett klientoperativsystem kumulativt konsekvent över den begärt frekvensen standard antal försök som för närvarande inställd på 9 internt av klienten inte finns tillräckligt; i det här fallet genererar klienten ett DocumentClientException med statuskoden 429 till programmet. Standardvärdet för antal återförsök kan ändras med hjälp av setRetryOptions på ConnectionPolicy-instansen. Som standard returneras DocumentClientException med statuskoden 429 efter en kumulativ väntetid på 30 sekunder om begäran fortsätter att fungera över frekvensen med begäran. Detta inträffar även när det aktuella antalet nya försök är mindre än det högsta antal försök antalet måste vara den standardvärdet 9 eller ett användardefinierat värde.

    När automatiska försök beteendet hjälper oss för att förbättra återhämtning och användbarhet för de flesta program, kan det ha följt emot varandra när du gör prestandatester, särskilt när mätning av svarstiden.  Svarstid för klient-observerade kommer ökar om experimentet träffar server-begränsning och orsakar klient-SDK ska försöka utföra tyst. För att undvika svarstidsspikar under prestanda experiment mäta kostnad som returneras av varje åtgärd och se till att begäranden fungerar nedan reserverade förfrågningar. Mer information finns i [programbegäran](request-units.md).
3. **Design för mindre dokument för högre genomströmning**

    Begäran-tillägget (kostnaden för behandling av begäranden) för en viss åtgärd korreleras direkt till samma storlek som dokumentet. Åtgärder för stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utformar ditt program för skalbarhet och hög prestanda finns [partitionering och skalning i Azure Cosmos DB](partition-data.md).
