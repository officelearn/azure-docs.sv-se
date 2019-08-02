---
title: Azure Cosmos DB prestanda tips för asynkron Java
description: Lär dig mer om klient konfigurations alternativ för att förbättra Azure Cosmos DB databas prestanda
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 2c2d776012e702469be4fd3217fb89be0ad419bf
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261620"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Prestanda tips för Azure Cosmos DB och asynkron Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [NET](performance-tips.md)
> 

Azure Cosmos DB är en snabb och flexibel distribuerad databas som skalar sömlöst med garanterad svars tid och data flöde. Du behöver inte göra större ändringar i arkitekturen eller skriva komplex kod för att skala databasen med Azure Cosmos DB. Att skala upp och ned är lika enkelt som att göra ett enda API-anrop eller SDK-metod anrop. Men eftersom Azure Cosmos DB nås via nätverks anrop finns det optimeringar på klient sidan som du kan göra för att uppnå högsta prestanda när du använder [SQL-asynkron Java SDK](sql-api-sdk-async-java.md).

Så om du frågar "Hur kan jag förbättra min databas prestanda?" Överväg följande alternativ:

## <a name="networking"></a>Nätverk
   <a id="same-region"></a>
1. **Samordna-klienter i samma Azure-region för prestanda**

    När det är möjligt kan du placera alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos DBs databasen. För en ungefärlig jämförelse kan anrop till Azure Cosmos DB inom samma region slutföras inom 1-2 MS, men svars tiden mellan västra USA och östra kust är > 50 ms. Den här fördröjningen kan troligt vis variera från begäran till begäran beroende på den väg som tas av begäran när den skickas från klienten till Azure Data Center-gränser. Den lägsta möjliga fördröjningen uppnås genom att se till att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB slut punkten. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).

    ![Bild av Azure Cosmos DB anslutnings princip](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK-användning
1. **Installera den senaste SDK: n**

    Azure Cosmos DB SDK: er har ständigt förbättrats för att ge bästa möjliga prestanda. Se de [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) -sidorna för att fastställa de senaste SDK: er och gransknings förbättringarna.
2. **Använd en singleton Azure Cosmos DB-klient för programmets livs längd**

    Varje AsyncDocumentClient-instans är tråd säker och utför effektiv anslutnings hantering och cachelagring av adresser. För att möjliggöra effektiv anslutnings hantering och bättre prestanda av AsyncDocumentClient rekommenderar vi att du använder en enda instans av AsyncDocumentClient per AppDomain för programmets livs längd.

   <a id="max-connection"></a>

3. **Justera ConnectionPolicy**

    Azure Cosmos DB begär Anden görs via HTTPS/REST när du använder async Java SDK och omfattas av den maximala maximala anslutningspoolen (1000). Standardvärdet bör vara idealiskt för majoriteten av användnings fallen. Men om du har en stor samling med många partitioner kan du ange max storleken för anslutningspoolen till ett större nummer (t. ex. 1500) med hjälp av setMaxPoolSize.

4. **Justera parallella frågor för partitionerade samlingar**

    Azure Cosmos DB SQL async Java SDK stöder parallella frågor som gör att du kan fråga en partitionerad samling parallellt. Mer information finns i [kod exempel](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) för att arbeta med SDK: er. Parallella frågor är utformade för att förbättra svars tid och data flöde för deras serie motsvarighet.

    (a) ***fin justering\: av setMaxDegreeOfParallelism*** -parallella frågor fungerar genom att fråga flera partitioner parallellt. Data från en enskild partitionerad samling hämtas dock i serie med avseende på frågan. Använd setMaxDegreeOfParallelism för att ställa in antalet partitioner som har maximal chans att uppnå den mest utförda frågan, förutsatt att alla andra system villkor är desamma. Om du inte känner till antalet partitioner kan du använda setMaxDegreeOfParallelism för att ange ett högt antal, och systemet väljer det lägsta (antal partitioner, indata från användaren) som den högsta graden av parallellitet.

    Det är viktigt att Observera att parallella frågor ger de bästa fördelarna om data är jämnt fördelade över alla partitioner med avseende på frågan. Om den partitionerade samlingen är partitionerad, så att alla eller en majoritet av de data som returneras av en fråga är koncentrerade i några partitioner (en partition i värsta fall), skulle prestandan för frågan bli Flask hals av dessa partitioner.

    (b) ***justering av\: setMaxBufferedItemCount*** parallell fråga är utformad för att hämta resultat när den aktuella gruppen med resultat bearbetas av klienten. För hämtning bidrar till den totala tids fördröjnings förbättringen av en fråga. setMaxBufferedItemCount begränsar antalet i förväg hämtade resultat. Om du anger setMaxBufferedItemCount till det förväntade antalet returnerade resultat (eller en högre siffra) kan frågan ta emot maximal nytta av för hämtning.

    För hämtning fungerar på samma sätt oavsett MaxDegreeOfParallelism, och det finns en enda buffert för data från alla partitioner.

5. **Implementera backoff med getRetryAfterInMilliseconds-intervall**

    Under prestanda testningen bör du öka belastningen tills en låg frekvens av begär Anden blir begränsad. Om detta är begränsat bör klient programmet backoff för det Server-angivna återförsöksintervallet. Genom att respektera backoff garanterar du att du tillbringar minimal tid på att vänta mellan återförsök.
6. **Skala ut din klient arbets belastning**

    Om du testar med höga data flödes nivåer (> 50000 RU/s) kan klient programmet bli Flask halsen på grund av att datorn capping ut på processor-eller nätverks användning. Om du når den här punkten kan du fortsätta att skicka det Azure Cosmos DB kontot ytterligare genom att skala ut dina klient program över flera servrar.

7. **Använd namn baserat adressering**

    Använd namnbaserade adressering, där länkar har formatet `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, i stället för SelfLinks (\_Self), som har formatet `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` för att undvika att hämta ResourceIds för alla resurser som används för att skapa länken. Som de här resurserna kommer att återskapas (eventuellt med samma namn) är det inte säkert att de cachelagrar dem.

   <a id="tune-page-size"></a>
8. **Justera sid storleken för frågor/läsa feeds för bättre prestanda**

    När du utför en Mass läsning av dokument med hjälp av funktionen för att läsa feeds (till exempel readDocuments) eller när du utfärdar en SQL-fråga, returneras resultatet i ett segment om resultat mängden är för stor. Som standard returneras resultaten i segment om 100 objekt eller 1 MB, beroende på vilken gräns som nåtts först.

    För att minska antalet nätverks fördröjningar som krävs för att hämta alla tillämpliga resultat kan du öka sid storleken med rubriken [x-MS-Max-item-Count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) till upp till 1000. I de fall där du bara behöver visa några få resultat, till exempel om ditt användar gränssnitt eller ditt program-API bara returnerar 10 resultat en gång, kan du också minska sid storleken till 10 för att minska data flödet som används för läsningar och frågor.

    Du kan också ange sid storleken med setMaxItemCount-metoden.

9. **Använd lämplig Scheduler (Undvik att stjäla händelse slingor i IO-nättrådar)**

    Den asynkrona Java SDK [](https://netty.io/) : n använder nettning för icke-blockerande i/o. SDK använder ett fast antal upprepnings trådar i IO-nettning (så många processor kärnor som datorn har) för att köra IO-åtgärder. Det observerbara som returneras av API: n genererar resultatet av en av de uppdelade IO-händelserna för den delade IO-händelsen. Det är därför viktigt att inte blockera den delade IO-händelsen av en loop. Att utföra processor intensiva arbeten eller blockera åtgärder i Netstore-tråden i IO-händelseloggen kan orsaka död läge eller avsevärt minska SDK-dataflödet.

    Följande kod kör till exempel ett processor intensivt arbete i-tråden för händelse slingor i IO:

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

    När resultatet har tagits emot, om du vill göra processor intensivt arbete på resultatet bör du undvika att göra det på händelse loop i/o-kontexten. Du kan i stället tillhandahålla din egen schemaläggare att tillhandahålla en egen tråd för att köra ditt arbete.

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

    Baserat på typen av arbete bör du använda den aktuella befintliga RxJava Scheduler för ditt arbete. Läs här [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Mer information finns på [GitHub-sidan](https://github.com/Azure/azure-cosmosdb-java) för ASYNKRON Java SDK.

10. **Inaktivera loggning** av netttjänster Loggning av Netstore-bibliotek är chatt och måste stängas av (under tryckning av inloggnings konfigurationen kanske inte räcker) för att undvika ytterligare processor kostnader. Om du inte är i fel söknings läge inaktiverar du nett loggning helt. Så om du använder Log4J för att ta bort de ytterligare CPU-kostnader som ``org.apache.log4j.Category.callAppenders()`` uppstår vid nettning lägger du till följande rad i kodbasen:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Resurs gräns för öppna filer i operativ systemet** Vissa Linux-system (till exempel Red Hat) har en övre gräns för antalet öppna filer och så det totala antalet anslutningar. Kör följande för att visa de aktuella gränserna:

    ```bash
    ulimit -a
    ```

    Antalet öppna filer (nofile) måste vara tillräckligt stort för att det ska finnas tillräckligt med plats för den konfigurerade anslutningspoolen och andra öppna filer av operativ systemet. Den kan ändras för att tillåta en större storlek på anslutningspoolen.

    Öppna filen Limits. conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Lägg till/ändra följande rader:

    ```
    * - nofile 100000
    ```

12. **Använd inbyggd SSL-implementering för nettning** Nettning kan använda OpenSSL direkt för SSL implementation stack för att uppnå bättre prestanda. I avsaknad av denna konfigurations-nettning kommer det att gå tillbaka till Javas standard-SSL-implementering.

    på Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    och Lägg till följande beroende till dina projekt maven-beroenden:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

För andra plattformar (Red Hat, Windows, Mac osv.), se dessa instruktioner https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indexeringspolicy
 
1. **Utesluta sökvägar som inte används från indexering för att få snabbare skrivning**

    Med Azure Cosmos DB indexerings principen kan du ange vilka dokument Sök vägar som ska tas med eller undantas från indexering genom att använda indexerings Sök vägar (setIncludedPaths och setExcludedPaths). Användningen av indexerings Sök vägar kan ge bättre skriv prestanda och lägre index lagring för scenarier där fråge mönstren är kända i förväg, eftersom indexerings kostnader direkt korreleras med antalet unika sökvägar som indexeras. Följande kod visar till exempel hur du undantar en hel del av dokumenten (kallas även ett under träd) från indexering med jokertecknet "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Mer information finns i [Azure Cosmos DB indexerings principer](indexing-policies.md).

## <a name="throughput"></a>Dataflöde
<a id="measure-rus"></a>

1. **Mått och justering för lägre enheter för programbegäran/andra användning**

    Azure Cosmos DB erbjuder en omfattande uppsättning databas åtgärder, inklusive relationella och hierarkiska frågor med UDF: er, lagrade procedurer och utlösare – allt som körs på dokumenten i en databas samling. Kostnaden som är kopplad till var och en av dessa åtgärder varierar beroende på processor, IO och minne som krävs för att slutföra åtgärden. I stället för att tänka på och hantera maskin varu resurser kan du tänka på en enhet för begäran (RU) som ett enda mått för de resurser som krävs för att utföra olika databas åtgärder och betjäna en programbegäran.

    Data flödet har allokerats baserat på antalet enheter för [programbegäran](request-units.md) som angetts för varje behållare. Den begärda enhets förbrukningen utvärderas som en taxa per sekund. Program som överskrider den allokerade enhets hastigheten för deras behållare är begränsade tills hastigheten sjunker under den allokerade nivån för behållaren. Om ditt program kräver en högre data flödes nivå kan du öka data flödet genom att tillhandahålla ytterligare enheter för programbegäran.

    Komplexiteten i en fråga påverkar hur många enheter för programbegäran som används för en åtgärd. Antalet predikat, typen av predikat, antalet UDF: er och storleken på käll data uppsättningen påverkar kostnaden för frågor.

    Om du vill mäta omkostnaderna för en åtgärd (skapa, uppdatera eller ta bort) kan du kontrol lera huvudet [x-MS-Request-avgift](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) för att mäta antalet enheter för programbegäran som används av dessa åtgärder. Du kan också titta på motsvarande RequestCharge-egenskap i ResourceResponse\<T > eller FeedResponse\<T >.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Begär ande avgiften som returnerades i den här rubriken är en bråkdel av ditt etablerade data flöde. Om du till exempel har 2000 RU/s etablerad, och om föregående fråga returnerar 1000 1 KB-dokument, är kostnaden för åtgärden 1000. I och med den här servern bevarar servern bara två sådana begär Anden innan hastigheten begränsar efterföljande begär Anden. Mer information finns i [enheter](request-units.md) för programbegäran och [Kalkylatorn för begär ande](https://www.documentdb.com/capacityplanner)enheter.
<a id="429"></a>
2. **Hastighets begränsning/begär ande frekvens för stor**

    När en klient försöker överskrida det reserverade data flödet för ett konto, finns det ingen prestanda försämring på servern och ingen användning av data flödes kapaciteten utöver den reserverade nivån. Servern kommer att förebyggande syfte avsluta begäran med RequestRateTooLarge (HTTP-statuskod 429) och returnera huvudet [x-MS-retry-efter-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) som anger hur lång tid i millisekunder som användaren måste vänta innan begäran försöker igen.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK: erna fångar alla implicita dessa svar, med den server-angivna återförsöket-efter-rubriken och gör om begäran. Om ditt konto inte kan nås samtidigt av flera klienter kommer nästa försök att lyckas.

    Om du har mer än en klient ackumulerad på ett konsekvent sätt över begär ande frekvensen är standard antalet nya försök som för närvarande är 9 internt av klienten inte tillräckligt. i det här fallet genererar klienten en DocumentClientException med status kod 429 i programmet. Standard antalet återförsök kan ändras genom att använda setRetryOptions på ConnectionPolicy-instansen. Som standard returneras DocumentClientException med status kod 429 efter en ackumulerad vänte tid på 30 sekunder om begäran fortsätter att köras över begär ande frekvensen. Detta inträffar även om det aktuella antalet återförsök är mindre än max antalet försök, måste det vara standardvärdet 9 eller ett användardefinierat värde.

    Även om det automatiserade återförsöket hjälper till att förbättra återhämtning och användbarhet för de flesta program, kan det komma på strider när du gör prestanda mått, särskilt när du mäter svars tid. Den klientbaserade svars tiden kommer att inaktive ras om experimentet träffar Server begränsningen och gör att klient-SDK: n kan försöka tyst igen. För att undvika fördröjningar vid prestanda experiment kan du mäta den avgift som returneras av varje åtgärd och se till att förfrågningarna fungerar under den reserverade begär ande frekvensen. Mer information finns i [enheter](request-units.md)för programbegäran.
3. **Design för mindre dokument för högre data flöde**

    Begär ande avgiften (bearbetnings kostnaden för begäran) för en specifik åtgärd är direkt korrelerad med dokumentets storlek. Åtgärder i stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utformar programmet för skalning och höga prestanda finns i [partitionering och skalning i Azure Cosmos DB](partition-data.md).
