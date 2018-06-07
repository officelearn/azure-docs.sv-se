---
title: Azure DB Cosmos Prestandatips för Java | Microsoft Docs
description: Läs klienten konfigurationsalternativ för att förbättra Azure Cosmos DB databasprestanda
keywords: hur du förbättrar databasens prestanda
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: sngun
ms.openlocfilehash: 92a7ed065b2ab29037e8c2467e210e7fd0ba3a07
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34613184"
---
> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Prestandatips för Azure Cosmos DB och Java
Azure Cosmos-DB är en snabb och flexibel distribuerad databas som kan skalas sömlöst med garanterad svarstid och genomströmning. Du behöver inte göra ändringar av större arkitektur eller skriva komplex kod för att skala din databas med Azure Cosmos DB. Skala upp och ner är lika enkelt som att göra en enda API-anrop eller [SDK-anrop](set-throughput.md#set-throughput-java). Men eftersom Azure Cosmos DB går att nå via nätverket anrop finns på klientsidan optimeringar kan du uppnå högsta prestanda när du använder den [SQL Java SDK](documentdb-sdk-java.md).

Så om du begär ”hur kan jag förbättra Mina databasprestanda”? Överväg följande alternativ:

## <a name="networking"></a>Nätverk
<a id="direct-connection"></a>

1. **Anslutningsläge: Använd DirectHttps**

    Hur en klient ansluter till Azure Cosmos DB har stor betydelse för prestanda, särskilt observerade klientens svarstid. Det finns en nyckel konfigurationsinställning användas för att konfigurera klienten [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) – [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode).  Det finns två tillgängliga ConnectionModes:

   1. [Gateway (standard)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.gateway)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.directhttps)

    Gateway-läge stöds på alla SDK-plattformar och är konfigurerad som standard.  Om programmet körs i ett företagsnätverk med strikt brandväggsbegränsningar, är Gateway det bästa valet eftersom den använder standardporten för HTTPS och en enda slutpunkt. Förhållandet prestanda är dock att Gateway-läge innebär ett hopp ytterligare nätverk varje gång data har lästs eller skrivits till Azure Cosmos DB. Därför ger DirectHttps läge bättre prestanda på grund av färre nätverkshopp. 

    Java SDK använder HTTPS som transportprotokoll. SSL används HTTPS för första autentiseringen och kryptering av trafik. När du använder Java SDK, måste endast HTTPS-port 443 vara öppen. 

    ConnectionMode konfigureras under konstruktion av DocumentClient-instans med parametern ConnectionPolicy. 

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

    När det är möjligt placera alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos-DB-databas. Anrop till Azure Cosmos DB inom samma region som utför inom 1 – 2 ms för en ungefärlig jämförelse, men fördröjning mellan Väst och av USA: S > 50 ms. Den här fördröjningen kan förmodligen variera från begäran till begäran beroende på vägen som begäran när det överförs från klienten till den Azure datacenter-gränsen. Lägsta möjliga tidsfördröjning uppnås genom att säkerställa att det anropande programmet finns i samma Azure-region som etablerade Azure DB som Cosmos-slutpunkt. En lista över tillgängliga regioner, se [Azure-regioner](https://azure.microsoft.com/regions/#services).

    ![Bild av principen för Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-användning
1. **Installera den senaste SDK**

    SDK: er för Azure Cosmos DB är ständigt bättre för att tillhandahålla bästa prestanda. Finns det [Azure Cosmos DB SDK](documentdb-sdk-java.md) sidor för att avgöra den senaste SDK och granska förbättringar.
2. **Använd klienten en singleton-Azure Cosmos DB för livslängden för ditt program**

    Varje [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) instansen är trådsäker och utför effektiv hantering och cachelagring av adresser när direkt läge. Om du vill tillåta effektiv hantering och bättre prestanda med DocumentClient, rekommenderas att använda en enda instans av DocumentClient per AppDomain för livslängden för programmet.

   <a id="max-connection"></a>
3. **Öka MaxPoolSize per värd när du använder läget för Gateway**

    Azure Cosmos-DB-begäranden görs över HTTPS/RESTEN när du använder Gateway-läge och är föremål för standard anslutningsgränsen per värdnamn eller IP-adress. Du kan behöva ange MaxPoolSize till ett högre värde (200-1000) så att klientbiblioteket kan använda flera samtidiga anslutningar till Azure Cosmos DB. I Java SDK, standardvärdet för [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.getmaxpoolsize) är 100. Använd [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize) att ändra värdet.

4. **Justera parallella frågor för partitionerade samlingar**

    Azure Cosmos DB SQL Java SDK version 1.9.0 och högre support parallella frågor, vilket gör det möjligt att fråga en partitionerad samling parallellt (se [arbeta med SDK: erna](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) och den relaterade [kodexempel](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) för Mer info). Parallella frågor är utformade för att förbättra svarstid och genomströmning över sin seriella motsvarighet.

    (a) ***justera setMaxDegreeOfParallelism\:***  parallella frågor arbete genom att fråga flera partitioner parallellt. Dock hämtas data från en enskild partitionerad samling seriellt med avseende på frågan. Därför använder [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism) ange antalet partitioner som har högsta risken för att uppnå den mest performant fråga, förutsatt att alla andra system villkoren förblir oförändrade. Om du inte vet antalet partitioner, du kan använda setMaxDegreeOfParallelism för att ange ett högre värde och minsta (antal partitioner, tillhandahålls användarindata) väljs automatiskt som högsta grad av parallellitet. 

    Det är viktigt att notera att parallella frågor ger bästa fördelarna om data är jämnt fördelad över alla partitioner med avseende på frågan. Om partitionerade samlingen är partitionerad så att hela eller en majoritet av data som returneras av en fråga samlas i några partitioner (en partition i värsta fall) och sedan dessa partitioner skulle flaskhals prestanda för frågan.

    (b) ***justera setMaxBufferedItemCount\:***  Parallel query är utformat för att före hämtar resultat när den aktuella batchen med resultat som behandlas av klienten. Det hjälper till att hämtas först i den övergripande svarstiden förbättring av en fråga. setMaxBufferedItemCount begränsar antalet före hämtade resultat. Genom att ange [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount) till det förväntade antalet resultat som returneras (eller fler) på så sätt kan frågan för att hämta det maximala fördelar.

    Hämta fungerar på samma sätt oavsett MaxDegreeOfParallelism och det finns en enda buffert för data från alla partitioner.  

5. **Implementera backoff med getRetryAfterInMilliseconds intervall**

    Under prestandatester, bör du öka belastningen tills en liten andel begäranden hämta begränsas. Om begränsas, bör klientprogrammet backoff på begränsning för intervallet-server har angetts. Respektera backoff garanterar att du ägnar minimal mängd väntetid mellan försöken. Stöd för återförsök Grupprincip ingår i Version 1.8.0 och senare av den [Java SDK](documentdb-sdk-java.md). Mer information finns i [Exceeding reserverat dataflöde gränser](request-units.md#RequestRateTooLarge) och [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds).
6. **Skala upp din klient arbetsbelastning**

    Om du testar på hög genomströmning nivåer (> 50 000 RU/s), klientprogrammet kan bli en flaskhals på grund av den datorn tak som skall ut på processor eller användning. Om du når den här punkten kan kan du fortsätta att skicka ytterligare Azure Cosmos DB kontot genom att skala ut ditt klientprogram på flera servrar.

7. **Använd namn baserat adressering**

    Använd namnbaserat adressering, där länkar har formatet `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, i stället för SelfLinks (\_self), som har formatet `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` att undvika att hämta ResourceIds för alla resurser som används för att skapa länken. Dessutom som resurserna hämta återskapas (eventuellt med samma namn) kan cachelagring av dessa inte hjälpa.

   <a id="tune-page-size"></a>
8. **Finjustera sidstorleken för frågor/läsa feeds för bättre prestanda**

    När utför en grupp läsa dokument med hjälp av Läs feed funktioner (till exempel [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c) eller vid utfärdande av en SQL-fråga, returneras resultatet i ett segmenterade sätt om resultatet är för stor. Resultaten returneras i mängder 100 objekt eller 1 MB som standard, oavsett vilken gränsen är träffar första.

    För att minska antalet nätverket förfrågningar krävs för att hämta alla tillämpliga resultat, kan du öka sidan storlek med den [x-ms-max--antal objekt](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) huvudet i begäran till upp till 1000. I fall där du vill visa endast några resultat, till exempel om ditt användar-gränssnittet eller programmet API returnerar bara 10 resulterar en tid, du kan också minska sidstorleken till 10 för att minska den används för läsning och frågor.

    Du kan också ange sidan storlek med den [setPageSize metoden](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer).

## <a name="indexing-policy"></a>Indexeringspolicy
 
1. **Uteslut oanvända sökvägar från indexering för snabbare skrivningar**

    Azure Cosmos DB indexprincip kan du ange vilka sökvägar dokument om du vill inkludera eller exkludera från indexering genom att använda indexering sökvägar ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths) och [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths)). Användning av indexering sökvägar kan erbjuda bättre skrivprestanda och lägre index lagring för scenarier där frågemönster är kända i förväg, som indexerings kostnader direkt kopplas ihop med antalet unika sökvägar som indexeras.  Följande kod visar exempelvis undanta (kallas även ett helt avsnitt av dokumenten en underkatalog) från att använda indexering av ”*” jokertecken.

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

    För mätning av alla åtgärder (skapa, uppdatera eller ta bort) inspektera den [x-ms-begäran-kostnad](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) huvud (eller motsvarande RequestCharge-egenskapen i [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response) eller [FeedResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response) att mäta antalet begäran enheter som används av dessa åtgärder.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Begäran-tillägget som returneras i det här sidhuvudet är en del av din dataflöde. Till exempel om du har 2000 RU/s etablerats och om den föregående frågan returnerar 1000 1KB-dokument, kostnaden för åtgärden är 1000. Inom en sekund godkänner servern därför bara två sådana begäranden innan begränsning efterföljande förfrågningar. Mer information finns i [programbegäran](request-units.md) och [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Hantera hastighet begränsa/förfrågningar för stor**

    När en klient försöker överskrida reserverat dataflöde för ett konto, finns det inga prestandaförsämring på servern och ingen användning av genomflödeskapaciteten utanför den reserverade. Servern förebyggande syfte kan avsluta begäran och RequestRateTooLarge (HTTP-statuskod 429) och returnera den [x-ms-retry-efter-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) huvud som anger hur lång tid i millisekunder som användaren måste vänta innan ett nytt försök begäran.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK: erna alla implicit catch-svaret respektera server angetts försök igen efter rubriken och försöka. Om ditt konto används samtidigt av flera klienter, lyckas nästa försök.

    Om du har mer än ett klientoperativsystem kumulativt konsekvent över den begärt frekvensen standard antal försök som för närvarande inställd på 9 internt av klienten inte finns tillräckligt; i så fall måste klienten genererar ett [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) med statusen code 429 till programmet. Standardvärdet för antal återförsök kan ändras med hjälp av [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions) på den [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) instans. Som standard returneras DocumentClientException med statuskoden 429 efter en kumulativ väntetid på 30 sekunder om begäran fortsätter att fungera över frekvensen med begäran. Detta inträffar även när det aktuella antalet nya försök är mindre än det högsta antal försök antalet måste vara den standardvärdet 9 eller ett användardefinierat värde.

    När automatiska försök beteendet hjälper oss för att förbättra återhämtning och användbarhet för de flesta program, kan det ha följt emot varandra när du gör prestandatester, särskilt när mätning av svarstiden.  Svarstid för klient-observerade kommer ökar om experimentet träffar server-begränsning och orsakar klient-SDK ska försöka utföra tyst. För att undvika svarstidsspikar under prestanda experiment mäta kostnad som returneras av varje åtgärd och se till att begäranden fungerar nedan reserverade förfrågningar. Mer information finns i [programbegäran](request-units.md).
3. **Design för mindre dokument för högre genomströmning**

    Begäran-tillägget (kostnaden för behandling av begäranden) för en viss åtgärd korreleras direkt till samma storlek som dokumentet. Åtgärder för stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utformar ditt program för skalbarhet och hög prestanda finns [partitionering och skalning i Azure Cosmos DB](partition-data.md).
