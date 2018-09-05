---
title: Azure Cosmos DB-Prestandatips för .NET | Microsoft Docs
description: Lär dig klienten konfigurationsalternativ för att förbättra prestanda för Azure Cosmos DB-databasen
keywords: hur vi kan förbättra databasens prestanda
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: sngun
ms.openlocfilehash: 47896b681c2ba43d4ca41682ec51e0dfd167c9d5
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700964"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Prestandatips för Azure Cosmos DB och .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB är en snabb och flexibel distribuerad databas som kan skalas sömlöst med garanterad svarstid och dataflöde. Du behöver inte göra ändringar i större arkitekturen eller skriva komplex kod för att skala din databas med Azure Cosmos DB. Skala upp och ned är lika enkelt som att göra en enda API-anrop eller [SDK-anrop](set-throughput.md#set-throughput-sdk). Eftersom Azure Cosmos DB nås via nätverksanrop det finns dock klientsidan optimeringar som du kan göra för att uppnå högsta prestanda när du använder den [SQL .NET SDK](documentdb-sdk-dotnet.md).

Så om du begär ”hur kan jag förbättra min databasprestanda”? Överväg följande alternativ:

## <a name="networking"></a>Nätverk
<a id="direct-connection"></a>

1. **Princip: Använd direkt anslutningsläge**

    Hur en klient ansluter till Azure Cosmos DB har stor betydelse för prestanda, särskilt när det gäller observerade klientens svarstid. Det finns två viktiga konfigurationsinställningar för att konfigurera klientens princip – anslutningen *läge* och [anslutning *protokollet*](#connection-protocol).  Det finns två tillgängliga lägen:

   * Gateway-läge (standard)
      
     Gateway-läge stöds på alla SDK-plattformar och är konfigurerad standard. Om programmet körs inifrån ett företagsnätverk med strikta brandväggsbegränsningar, är Gateway-läget det bästa valet eftersom det använder HTTPS-standardport och en enda slutpunkt. Prestanda-Nackdelen är dock att Gateway-läge innebär att en ytterligare ett hopp varje gång som data har lästs eller skrivits till Azure Cosmos DB. Därför erbjuder bättre prestanda på grund av färre nätverkssteg i direkt-läge. Gateway-anslutningsläge rekommenderas när du kör program i miljöer med begränsat antal socketanslutningar, till exempel när du använder Azure Functions eller om du är i en förbrukningsplan. 

   * Direkt-läge

     Direkt-läge stöder anslutningar via TCP- och HTTPS-protokoll. För närvarande stöds direkt i .NET Standard 2.0 för Windows-plattformen. När du använder direkt läge, finns det två protokollalternativ:

    * TCP
    * HTTPS

    När du använder Gateway-läge, Azure Cosmos DB använder port 443 och MongoDB-API använder 10250, 10255 och 10256 portar. 10250 port mappas till en Mongodb-instans som ska användas som standard utan geo-replikering och 10255/10256 portar mappas till Mongodb-instans med funktioner för geo-replikering. När du använder TCP i direkt läge, förutom portarna som Gateway, måste du kontrollera porten intervallet mellan 10000 och 20000 är öppen eftersom Azure Cosmos DB använder dynamiska TCP-portar. Om inte dessa portar är öppna och du försöker använda TCP, felmeddelandet 503 tjänsten är inte tillgänglig. I följande tabell visar anslutningslägen som är tillgängliga för olika API: er och portar tjänstanvändaren för varje API:

    |Anslutningsläge  |Protokoll som stöds  |Stödda SDK: erna  |API/Service-port  |
    |---------|---------|---------|---------|
    |Gateway  |   HTTPS    |  Alla SDK: er    |   SQL(443) Mongo (10250, 10255, 10256), Table(443), Cassandra(443), Graph(443)    |
    |Direkt    |    HTTPS     |  .NET och Java SDK    |    SQL(443)   |
    |Direkt    |     TCP    |  .NET SDK    | Portar i intervallet 20 10 000 000 |

    Azure Cosmos DB erbjuder en enkel och öppna RESTful-programmeringsmiljö via HTTPS. Dessutom finns det en effektiv TCP-protokollet som är också RESTful i sin modell och är tillgänglig via SDK för .NET-klient. Använda SSL för den inledande autentiseringen och kryptering trafik direkt TCP- och HTTPS. Använda TCP-protokollet när det är möjligt för bästa prestanda.

    Anslutningsläget konfigureras under konstruktionen av DocumentClient-instansen med parametern ConnectionPolicy. Om du använder direkt läge anges protokollet också i parametern ConnectionPolicy.

    ```csharp
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    Eftersom TCP stöds endast i direkt läge om Gateway-läge används, HTTPS-protokollet används alltid för att kommunicera med gatewayen och Protocol-värdet i ConnectionPolicy ignoreras.

    ![Bild av principen för Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Anropa OpenAsync för att undvika Start svarstid på första begäran**

    Den första begäran har en högre latens eftersom den har att hämta adress routningstabellen som standard. För att undvika den här fördröjningen för start på den första begäran kan anropa du OpenAsync() en gång under initieringen på följande sätt.

        await client.OpenAsync();
   <a id="same-region"></a>
3. **Samordna klienter i samma Azure-region för prestanda**

    Om det är möjligt placera alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos DB-databasen. En ungefärlig jämförelse anrop till Azure Cosmos DB inom samma region som slutförs inom 1 – 2 ms, men fördröjningen mellan västra och östra västkust är > 50 ms. Den här fördröjningen kan förmodligen variera från begäran till begäran beroende på vilken väg begäran när det överförs från klienten till Azure-datacenter-gränsen. Lägsta möjliga tidsfördröjning uppnås genom att kontrollera att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB-slutpunkten. En lista över tillgängliga regioner finns i [Azure-regionerna](https://azure.microsoft.com/regions/#services).

    ![Bild av principen för Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Öka antalet trådar/uppgifter**

    Eftersom anrop till Azure Cosmos DB görs över nätverket, kan du behöva variera graden av parallellitet över dina önskemål så att klientprogrammet använder mycket lite tid att vänta mellan begäranden. Exempel: Om du använder. NET'S [parallella Uppgiftsbibliotek](https://msdn.microsoft.com//library/dd460717.aspx), skapa i den ordning som 100-tal uppgifter läsning eller skrivning till Azure Cosmos DB.

## <a name="sdk-usage"></a>SDK-användning
1. **Installera den senaste SDK**

    Azure Cosmos DB SDK är ständigt bättre för att ge bästa möjliga prestanda. Se den [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) sidor för att fastställa den senaste SDK och granska förbättringar.
2. **Använda en singleton Azure Cosmos DB-klient under hela programmets livslängd**

    Varje instans av DocumentClient är trådsäker och utför effektiv anslutningshanteringen och cachelagring av adresser vid användning i direkt-läge. För att tillåta effektiv anslutningshanteringen och bättre prestanda genom att DocumentClient, rekommenderar vi att du använder en enda instans av DocumentClient per AppDomain för hela programmets livslängd.

   <a id="max-connection"></a>
3. **Öka System.Net MaxConnections per värd i Gateway-läge**

    Azure Cosmos DB begär görs över HTTPS/REST i Gateway-läge och har utsatts för Standardgränsen för anslutning per värdnamn eller IP-adress. Du kan behöva ställa in MaxConnections på ett högre värde (100-1000) så att klientbiblioteket kan använda flera samtidiga anslutningar till Azure Cosmos DB. I .NET SDK 1.8.0 och senare, standardvärdet för [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) är 50 och du kan ange om du vill ändra värdet i [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)på ett högre värde.   
4. **Justering parallella frågor partitionerade samlingar**

     SQL .NET SDK version 1.9.0 och högre support parallella frågor, som gör det möjligt att fråga en partitionerad samling parallellt (se [arbeta med SDK: erna](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) och de relaterade [kodexempel](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) för mer information). Parallella frågor är utformade för att förbättra svarstid och dataflöde över sin seriella motsvarighet. Parallella frågor ange två parametrar som användare kan justera för att skräddarsy den efter deras krav, (a) MaxDegreeOfParallelism: att styra det maximala antalet partitioner sedan kan efterfrågas parallellt och (b) MaxBufferedItemCount: att styra antalet före hämtade resultat.

    (a) ***justering MaxDegreeOfParallelism\:***  parallell fråga fungerar genom att fråga flera partitioner parallellt. Dock hämtas data från en enskild partitionerade samla in seriellt med avseende på frågan. Så, antalet partitioner att ställa in MaxDegreeOfParallelism har högsta risken för att uppnå de flesta högpresterande frågan, förutsatt att alla andra system villkor är desamma. Om du inte vet hur många partitioner kan du ange MaxDegreeOfParallelism till ett stort antal och minsta (antal partitioner, tillhandahålls användarindata) som MaxDegreeOfParallelism väljs automatiskt.

    Det är viktigt att notera att parallella frågor ger bästa fördelarna om data är jämnt fördelat över alla partitioner med avseende på frågan. Om partitionerade samlingen är partitionerad så att hela eller en merparten av de data som returneras av en fråga är koncentrerade i några partitioner (en partition i värsta fall) och sedan prestanda hos frågan skulle att skapa en flaskhals eftersom dessa partitioner.

    (b) ***justering MaxBufferedItemCount\:***  parallell har utformats för att hämta förväg resultat när den aktuella batchen med resultat som behandlas av klienten. Den förhämtning hjälper i den övergripande svarstiden förbättring av en fråga. MaxBufferedItemCount är parametern för att begränsa antalet resultat som förväg hämtade. Om du MaxBufferedItemCount till det förväntade antalet resultat som returneras (eller fler) kan frågan för att få största möjliga nytta från förhämtning.

    Förhämtning fungerar på samma sätt oavsett MaxDegreeOfParallelism och det finns en enda buffert för data från alla partitioner.  
5. **Aktivera serversidan GC**

    Minska frekvensen för skräpinsamling kan hjälpa att i vissa fall. I .NET, ställer du in [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) till true.
6. **Implementera backoff med RetryAfter intervall**

    Prestandatester, bör du öka belastningen tills en liten andel begäranden begränsas. Om prestandan för bör klientprogrammet backoff på begränsning för server angiven återförsöksintervallet. Följer backoff säkerställer att du ägnar kortast möjliga tid att vänta mellan försöken. Återförsök princip support ingår i versionen 1.8.0 och senare av SQL [.NET](sql-api-sdk-dotnet.md) och [Java](sql-api-sdk-java.md), version 1.9.0 och högre av de [Node.js](sql-api-sdk-node.md) och [Python](sql-api-sdk-python.md), och alla versioner av stöds i [.NET Core](sql-api-sdk-dotnet-core.md) SDK: er. Mer information finns i [Exceeding reserverade dataflödesbegränsningar](request-units.md#RequestRateTooLarge) och [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    Med version 1.19 och senare av .NET SDK finns en mekanism för att logga in ytterligare diagnostikinformation och felsöka svarstidsproblem enligt följande exempel. Du kan logga diagnostiska strängen för begäranden som har en högre lässvarstid. Hämtade diagnostiska strängen hjälper dig att förstå hur många gånger som du såg 429s för en viss begäran.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Skala ut din klient-arbetsbelastning**

    Om du testar på hög genomströmning nivåer (> 50 000 RU/s), klientprogrammet kan bli en flaskhals på grund av den datorn tak som skall ut på CPU- eller användning. Om du når den här punkten kan fortsätta du att skicka ytterligare Azure Cosmos DB-kontot genom att skala ut dina klientprogram på flera servrar.
8. **Cachelagra dokumentet URI: er för lägre lässvarstid**

    Cachelagra dokumentet URI: er när det är möjligt för bästa prestanda för läsning. Du måste definiera logik för att cachelagra resourceid när du skapar resursen. ResourceId baserade sökningar är snabbare än datornamnsbaserad sökningar, så cachelagring av dessa värden förbättrar prestandan. 

   <a id="tune-page-size"></a>
1. **Finjustera sidstorleken för frågor/läsning feeds för bättre prestanda**

    När du utför en massinläsning läsa dokument genom att använda Läs feed-funktioner (till exempel ReadDocumentFeedAsync) eller när en SQL-fråga, returneras resultatet i ett segmenterade sätt om resultatet är för stor. Resultaten returneras i segment om 100 objekt eller 1 MB som standard, uppnås för beroende på vilken gräns först.

    För att minska antalet nätverk nätverksförfrågningar som krävs för att hämta alla tillämpliga resultat, kan du öka storleken sida med [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) huvudet i begäran till upp till 1000. I fall där du vill visa endast några resultat, till exempel, om ditt användar-gränssnittet eller ett program-API returnerar endast 10 resulterar en tid, du kan också minska sidstorleken till 10 för att minska det dataflöde som används för läsningar och frågor.

    Du kan också ange sidstorleken med tillgängliga Azure Cosmos DB SDK.  Exempel:

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Öka antalet trådar/uppgifter**

    Se [öka antalet trådar/uppgifter](#increase-threads) i avsnittet nätverk.

11. **Använd 64-bitars värd bearbetning**

    SQL-SDK fungerar i en 32-bitars värdprocess när du använder SQL .NET SDK version 1.11.4 och senare. Om du använder flera partition frågor rekommenderas 64-bitars värd bearbetning för bättre prestanda. Följande typer av program har 32-bitars värdprocess som standard, så för att kunna ändra det till 64-bitars, Följ dessa steg beroende på vilken typ av ditt program:

    - För körbara program, detta kan göras genom att avmarkera den **föredrar 32-bitars** alternativet i den **Projektegenskaper** fönstret på den **skapa** fliken.

    - För VSTest efter testprojekt, kan du göra det genom att välja **testa**->**Testinställningar**->**standard processorarkitektur som X64**, från den **Visual Studio Test** menyalternativ.

    - För lokalt distribuerade ASP.NET-webbprogram, detta kan göras genom att markera den **använder 64-bitarsversionen av IIS Express för webbplatser och projekt**under **verktyg**->**alternativ**  -> **Produkter och lösningar**->**Web projekt**.

    - För ASP.NET-webbprogram distribueras på Azure, detta kan göras genom att välja den **plattform som 64-bitars** i den **programinställningar** på Azure portal.

## <a name="indexing-policy"></a>Indexeringspolicy
 
1. **Undanta oanvända sökvägar från indexering för snabbare skrivningar**

    Cosmos DB: s indexeringsprincip kan du ange vilka dokument sökvägar för att inkludera eller exkludera från indexering genom att använda indexering sökvägar (IndexingPolicy.IncludedPaths och IndexingPolicy.ExcludedPaths). Användning av indexering sökvägar kan erbjuda bättre skrivprestanda och lagring med lägre index för scenarier där frågemönstren är kända i förväg, som korreleras indexering kostnader direkt till antal unika sökvägar som indexeras.  Till exempel visar följande kod hur du undantar en hela avsnittet dokument (alias) ett underträd) från indexering med den ”*” med jokertecken.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Mer information finns i [Azure Cosmos DB indexeringsprinciper](indexing-policies.md).

## <a name="throughput"></a>Dataflöde
<a id="measure-rus"></a>

1. **Mät och justering för lägre begäran begärandeenheter/sekund användning**

    Azure Cosmos DB erbjuder en omfattande uppsättning databasoperationer, inklusive Relations- och Hierarkifrågor med användardefinierade funktioner, lagrade procedurer och utlösare – allt opererar på dokumenten i en databassamling. Den kostnad som hör till var och en av dessa operationer varierar beroende på CPU, IO och minne som krävs för att slutföra åtgärden. I stället för att tänka på och hantera maskinvaruresurser kan tänka du på en begäransenhet (RU) som det enda måttet på de resurser som krävs för att utföra olika databasoperationer och behandla en programbegäran.

    Dataflöde etableras baserat på antalet [programbegäran](request-units.md) för varje behållare. Konsumtion för begäran om enheten utvärderas som en avgift per sekund. Program som överstiger den etablerade begäranhastigheten för sina behållare är begränsade tills frekvensen sjunker under den etablerade nivån för behållaren. Om programmet kräver högre dataflöde kan öka du dataflödet genom att etablera ytterligare enheter för programbegäran. 

    Komplexiteten för en fråga påverkar hur många enheter för programbegäran som förbrukas för en åtgärd. Antal predikat, natur predikat, antal UDF: er och storleken på källan datauppsättningen alla påverkar kostnaden för frågeåtgärder.

    Att mäta arbetet med att alla åtgärder (skapa, uppdatera eller ta bort) granska de [x-ms-begäran-kostnad](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) rubrik (eller motsvarande RequestCharge-egenskapen i ResourceResponse<T> eller FeedResponse<T> i den. NET SDK) att mäta antalet enheter för programbegäran som förbrukas av de här åtgärderna.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    Avgiften för begäran som returnerades i det här sidhuvudet är en bråkdel av ditt etablerade dataflöde (d.v.s. 2000 ru / sekund). Om den föregående frågan returnerar 1000 1KB-dokument, är kostnaden för åtgärden 1000. Inom en sekund godkänner servern därför bara två sådana begäranden innan hastighet som begränsar efterföljande förfrågningar. Mer information finns i [programbegäran](request-units.md) och [begäran enhet Kalkylatorn](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Hantera hastighet begränsar/begärandehastighet för stor**

    När en klient försöker överskrida reserverat dataflöde för ett konto, finns det inga prestandaförsämring på servern och ingen användning av dataflödeskapacitet utöver nivån som är reserverade. Servern kommer sluta på begäran med RequestRateTooLarge (HTTP-statuskod 429) och returnera den [x-ms-återförsök-efter-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) huvud som anger hur lång tid i millisekunder som användaren måste vänta innan ett nytt försök begäran.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK: erna alla implicit fånga upp svaret, respekterar det server angiven sidhuvudet retry-after och försök begäran. Om inte ditt konto är samtidigt som används av flera klienter, lyckas nästa återförsök.

    Om du har mer än en för klienten kumulativt konsekvent ovan blir förfrågningsfrekvensen Standardantal försök som för närvarande inställd på 9 internt av klienten inte finns tillräckligt; i det här fallet genererar klienten ett DocumentClientException med statuskoden 429 till programmet. Standardvärdet för antal återförsök kan ändras genom att ange RetryOptions på ConnectionPolicy-instans. Som standard returneras DocumentClientException med statuskoden 429 efter en kumulativ väntetid på 30 sekunder om begäran fortsätter att fungera ovan blir förfrågningsfrekvensen. Detta inträffar även när det aktuella antalet återförsök är mindre än antalet försök, oavsett om det är standardvärdet 9 eller ett användardefinierat värde.

    När det automatiska återförsöksbeteendet hjälper oss för att förbättra återhämtning och användbarhet för de flesta program, kan det ha följt emot varandra när du gör prestandamått, särskilt när mäta svarstiden.  Svarstid för klient-observerade att utnyttja om experimentet som kommer till server-begränsning och leder till att klienten SDK att göra om tyst. Mät den kostnad som returneras av varje åtgärd för att undvika svarstidsspikar under prestanda experiment, och se till att begäranden fungerar nedan reserverade begäranhastigheten. Mer information finns i [programbegäran](request-units.md).
3. **Design för mindre dokument för högre dataflöde**

    Kostnaden för begäran (d.v.s. begäranbearbetningen kostnad) för en viss åtgärd korreleras direkt till storleken på dokumentet. Åtgärder för stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg
Ett exempelprogram som används för att utvärdera Azure Cosmos DB för högpresterande scenarier på några klientdatorer, se [prestanda och skalningstester med Azure Cosmos DB](performance-testing.md).

Se även om du vill veta mer om hur du utformar ditt program för skalning och hög prestanda, [partitionering och skalning i Azure Cosmos DB](partition-data.md).
