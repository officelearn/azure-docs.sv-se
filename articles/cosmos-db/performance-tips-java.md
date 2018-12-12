---
title: Azure Cosmos DB-Prestandatips för Java
description: Lär dig klienten konfigurationsalternativ för att förbättra prestanda för Azure Cosmos DB-databasen
keywords: hur vi kan förbättra databasens prestanda
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: sngun
ms.openlocfilehash: afbeb8211baea6da363a5e0162e92a0588581346
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872993"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Prestandatips för Azure Cosmos DB och Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [NET](performance-tips.md)
> 

Azure Cosmos DB är en snabb och flexibel distribuerad databas som kan skalas sömlöst med garanterad svarstid och dataflöde. Du behöver inte göra ändringar i större arkitekturen eller skriva komplex kod för att skala din databas med Azure Cosmos DB. Skala upp och ned är lika enkelt som att göra en enda API-anrop. Mer information finns i [hur du etablerar dataflöden i behållare](how-to-provision-container-throughput.md) eller [hur du etablerar databasen dataflöde](how-to-provision-database-throughput.md). Eftersom Azure Cosmos DB nås via nätverksanrop det finns dock klientsidan optimeringar som du kan göra för att uppnå högsta prestanda när du använder den [SQL Java SDK](documentdb-sdk-java.md).

Så om du begär ”hur kan jag förbättra min databasprestanda”? Överväg följande alternativ:

## <a name="networking"></a>Nätverk
<a id="direct-connection"></a>

1. **Anslutningsläge: Använd DirectHttps**

    Hur en klient ansluter till Azure Cosmos DB har stor betydelse för prestanda, särskilt när det gäller observerade klientens svarstid. Det finns en nyckeln är konfigurerad inställning är tillgänglig för att konfigurera klienten [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) – den [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode).  Det finns två tillgängliga ConnectionModes:

   1. [Gateway (standard)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode)

    Gateway-läge stöds på alla SDK-plattformar och är konfigurerad standard.  Om programmet körs inifrån ett företagsnätverk med strikta brandväggsbegränsningar, är Gateway det bästa valet eftersom det använder HTTPS-standardport och en enda slutpunkt. Prestanda-Nackdelen är dock att Gateway-läge innebär att en ytterligare ett hopp varje gång som data har lästs eller skrivits till Azure Cosmos DB. Därför erbjuder bättre prestanda på grund av färre nätverkssteg i DirectHttps läge. 

    Java SDK använder HTTPS som transport-protokoll. HTTPS använder SSL för den inledande autentiseringen och kryptering av trafik. När du använder Java SDK, måste endast HTTPS-port 443 är öppna. 

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

    ![Bild av principen för Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Samordna klienter i samma Azure-region för prestanda**

    Om det är möjligt placera alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos DB-databasen. En ungefärlig jämförelse anrop till Azure Cosmos DB inom samma region som slutförs inom 1 – 2 ms, men fördröjningen mellan västra och östra västkust är > 50 ms. Den här fördröjningen kan förmodligen variera från begäran till begäran beroende på vilken väg begäran när det överförs från klienten till Azure-datacenter-gränsen. Lägsta möjliga tidsfördröjning uppnås genom att kontrollera att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB-slutpunkten. En lista över tillgängliga regioner finns i [Azure-regionerna](https://azure.microsoft.com/regions/#services).

    ![Bild av principen för Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-användning
1. **Installera den senaste SDK**

    Azure Cosmos DB SDK är ständigt bättre för att ge bästa möjliga prestanda. Se den [Azure Cosmos DB SDK](documentdb-sdk-java.md) sidor för att fastställa den senaste SDK och granska förbättringar.
2. **Använda en singleton Azure Cosmos DB-klient under hela programmets livslängd**

    Varje [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) instansen är trådsäker och utför effektiv anslutningshanteringen och cachelagring av adresser vid användning i direkt-läge. För att tillåta effektiv anslutningshanteringen och bättre prestanda genom att DocumentClient, rekommenderar vi att du använder en enda instans av DocumentClient per AppDomain för hela programmets livslängd.

   <a id="max-connection"></a>
3. **Öka MaxPoolSize per värd i Gateway-läge**

    Azure Cosmos DB begär görs över HTTPS/REST i Gateway-läge och har utsatts för Standardgränsen för anslutning per värdnamn eller IP-adress. Du kan behöva ställa in MaxPoolSize på ett högre värde (200-1000) så att klientbiblioteket kan använda flera samtidiga anslutningar till Azure Cosmos DB. I Java SDK standardvärdet för [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.getmaxpoolsize) är 100. Använd [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize) att ändra värdet.

4. **Justering parallella frågor partitionerade samlingar**

    Azure Cosmos DB SQL Java SDK-version 1.9.0 och högre support parallella frågor, som gör det möjligt att fråga en partitionerad samling parallellt. Mer information finns i [kodexempel](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) gäller när du arbetar med SDK: erna. Parallella frågor är utformade för att förbättra svarstid och dataflöde över sin seriella motsvarighet.

    (a) ***justering setMaxDegreeOfParallelism\:***  parallella frågor arbete genom att fråga flera partitioner parallellt. Dock hämtas data från en enskild partitionerad samling seriellt med avseende på frågan. Därför använder [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism) anges att ange hur många partitioner som har högsta risken för att uppnå de mest högpresterande fråga, alla andra system villkor är desamma. Om du inte vet hur många partitioner, du kan använda setMaxDegreeOfParallelism för att ange ett högre värde och systemet väljer minst (antal partitioner, tillhandahålls användarindata) som högsta grad av parallellitet. 

    Det är viktigt att notera att parallella frågor ger bästa fördelarna om data är jämnt fördelat över alla partitioner med avseende på frågan. Om partitionerade samlingen är partitionerad så att hela eller en merparten av de data som returneras av en fråga är koncentrerade i några partitioner (en partition i värsta fall) och sedan prestanda hos frågan skulle att skapa en flaskhals eftersom dessa partitioner.

    (b) ***justering setMaxBufferedItemCount\:***  parallell har utformats för att hämta förväg resultat när den aktuella batchen med resultat som behandlas av klienten. Den förhämtning hjälper i den övergripande svarstiden förbättring av en fråga. setMaxBufferedItemCount begränsar antalet förväg hämtade resultat. Genom att ange [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount) till det förväntade antalet resultat som returneras (eller fler) Detta gör att frågan för att få största möjliga nytta från förhämtning.

    Förhämtning fungerar på samma sätt oavsett MaxDegreeOfParallelism och det finns en enda buffert för data från alla partitioner.  

5. **Implementera backoff med getRetryAfterInMilliseconds intervall**

    Prestandatester, bör du öka belastningen tills en liten andel begäranden begränsas. Om prestandan för bör klientprogrammet backoff på begränsning för server angiven återförsöksintervallet. Följer backoff säkerställer att du ägnar kortast möjliga tid att vänta mellan försöken. Återförsök princip support ingår i versionen 1.8.0 och senare av den [Java SDK](documentdb-sdk-java.md). Mer information finns i [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds).

6. **Skala ut din klient-arbetsbelastning**

    Om du testar på hög genomströmning nivåer (> 50 000 RU/s), klientprogrammet kan bli en flaskhals på grund av den datorn tak som skall ut på CPU- eller användning. Om du når den här punkten kan fortsätta du att skicka ytterligare Azure Cosmos DB-kontot genom att skala ut dina klientprogram på flera servrar.

7. **Använda namn baserat adressering**

    Använda namnbaserat adressering, där länkar har formatet `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, i stället för SelfLinks (\_själv), som har formatet `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` att undvika hämtar ResourceIds över alla resurser som används för att konstruera länken. Dessutom som resurserna hämta återskapas (eventuellt med samma namn), kan cachelagring av dessa inte hjälpa.

   <a id="tune-page-size"></a>
8. **Finjustera sidstorleken för frågor/läsning feeds för bättre prestanda**

    När utför en massinläsning läsa dokument med hjälp av Läs feed-funktioner (till exempel [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c) eller när en SQL-fråga, resultaten returneras i ett segmenterade sätt om resultatet är för stor. Resultaten returneras i segment om 100 objekt eller 1 MB som standard, uppnås för beroende på vilken gräns först.

    För att minska antalet nätverk nätverksförfrågningar som krävs för att hämta alla tillämpliga resultat, kan du öka storleken sida med de [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) huvudet i begäran till upp till 1000. I fall där du vill visa endast några resultat, till exempel, om ditt användar-gränssnittet eller ett program-API returnerar endast 10 resulterar en tid, du kan också minska sidstorleken till 10 för att minska det dataflöde som används för läsningar och frågor.

    Du kan också ange sida storlek med den [setPageSize metoden](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer).

## <a name="indexing-policy"></a>Indexeringspolicy
 
1. **Undanta oanvända sökvägar från indexering för snabbare skrivningar**

    Indexeringsprincip för Azure Cosmos DB kan du ange vilka dokument sökvägar för att inkludera eller exkludera från indexering genom att använda indexering sökvägar ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths) och [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths)). Användning av indexering sökvägar kan erbjuda bättre skrivprestanda och lagring med lägre index för scenarier där frågemönstren är kända i förväg, som korreleras indexering kostnader direkt till antal unika sökvägar som indexeras.  Till exempel visar följande kod hur du undantar en hela avsnittet dokument (alias) ett underträd) från indexering med den ”*” med jokertecken.

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

    Att mäta arbetet med att alla åtgärder (skapa, uppdatera eller ta bort), granska de [x-ms-begäran-kostnad](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) rubrik (eller motsvarande RequestCharge-egenskapen i [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response) eller [FeedResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response) att mäta antalet enheter för programbegäran som förbrukas av de här åtgärderna.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Kostnad för begäran som returnerades i det här sidhuvudet är en del av det tilldelade dataflödet. Till exempel om du har 2000 etablerade RU/s och om den föregående frågan returnerar 1000 1KB-dokument, kostnaden för åtgärden är 1 000. Inom en sekund godkänner servern därför bara två sådana begäranden innan hastighet som begränsar efterföljande förfrågningar. Mer information finns i [programbegäran](request-units.md) och [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
1. **Hantera hastighet begränsar/begärandehastighet för stor**

    När en klient försöker överskrida reserverat dataflöde för ett konto, finns det inga prestandaförsämring på servern och ingen användning av dataflödeskapacitet utöver nivån som är reserverade. Servern kommer sluta på begäran med RequestRateTooLarge (HTTP-statuskod 429) och returnera den [x-ms-återförsök-efter-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) huvud som anger hur lång tid i millisekunder som användaren måste vänta innan ett nytt försök begäran.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK: erna alla implicit fånga upp svaret, respekterar det server angiven sidhuvudet retry-after och försök begäran. Om inte ditt konto är samtidigt som används av flera klienter, lyckas nästa återförsök.

    Om du har mer än en för klienten kumulativt konsekvent ovan blir förfrågningsfrekvensen Standardantal försök som för närvarande inställd på 9 internt av klienten inte finns tillräckligt; i det här fallet klienten genererar ett [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) med status code 429 till programmet. Standardvärdet för antal återförsök kan ändras med hjälp av [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions) på den [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) instans. Som standard returneras DocumentClientException med statuskoden 429 efter en kumulativ väntetid på 30 sekunder om begäran fortsätter att fungera ovan blir förfrågningsfrekvensen. Detta inträffar även när det aktuella antalet återförsök är mindre än antalet försök, oavsett om det är standardvärdet 9 eller ett användardefinierat värde.

    När det automatiska återförsöksbeteendet hjälper oss för att förbättra återhämtning och användbarhet för de flesta program, kan det ha följt emot varandra när du gör prestandamått, särskilt när mäta svarstiden.  Svarstid för klient-observerade att utnyttja om experimentet som kommer till server-begränsning och leder till att klienten SDK att göra om tyst. Mät den kostnad som returneras av varje åtgärd för att undvika svarstidsspikar under prestanda experiment, och se till att begäranden fungerar nedan reserverade begäranhastigheten. Mer information finns i [programbegäran](request-units.md).
3. **Design för mindre dokument för högre dataflöde**

    Kostnaden för begäran (kostnaden för bearbetning av begäran) för en viss åtgärd korreleras direkt till storleken på dokumentet. Åtgärder för stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utformar ditt program för skalning och hög prestanda, finns i [partitionering och skalning i Azure Cosmos DB](partition-data.md).
