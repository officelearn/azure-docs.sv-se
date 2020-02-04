---
title: Azure Cosmos DB prestanda tips för .NET
description: Lär dig mer om klient konfigurations alternativ för att förbättra prestanda för Azure Cosmos Database
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: eec5ab6cdf4afd63db2e77046bb19436e600ece6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721004"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Prestanda tips för Azure Cosmos DB och .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB är en snabb och flexibel distribuerad databas som skalar sömlöst med garanterad svars tid och data flöde. Du behöver inte göra större ändringar i arkitekturen eller skriva komplex kod för att skala databasen med Azure Cosmos DB. Att skala upp och ned är lika enkelt som att göra ett enda API-anrop. Mer information finns i [så här etablerar du behållar data flöde](how-to-provision-container-throughput.md) eller [hur du etablerar databas data flöde](how-to-provision-database-throughput.md). Men eftersom Azure Cosmos DB nås via nätverks anrop finns det optimeringar på klient sidan som du kan göra för att uppnå högsta prestanda när du använder [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Så om du frågar "Hur kan jag förbättra min databas prestanda?" Överväg följande alternativ:

## <a name="networking"></a>Nätverk
<a id="direct-connection"></a>

1. **Anslutnings princip: Använd direkt anslutnings läge**

    Hur en klient ansluter till Azure Cosmos DB har stor inverkan på prestandan, särskilt de svarstider som uppstår på klientsidan. Det finns två nyckel konfigurations inställningar som är tillgängliga för konfigurering av klient anslutnings principer – anslutnings *läge* och anslutnings *protokoll*.  De två tillgängliga lägena är:

   * Gateway-läge
      
     Gateway-läge stöds på alla SDK-plattformar och är det konfigurerade standardvärdet för [Microsoft. Azure. DOCUMENTDB SDK](sql-api-sdk-dotnet.md). Om ditt program körs i ett företags nätverk med strikta brand Väggs begränsningar är Gateway-läget det bästa valet eftersom det använder standard-HTTPS-porten och en enda slut punkt. Prestanda kompromissen är dock att Gateway-läget omfattar ytterligare nätverks hopp varje gång data läses eller skrivs till Azure Cosmos DB. Därför erbjuder Direct-läget bättre prestanda på grund av färre nätverks hopp. Anslutnings läget för gateway rekommenderas också när du kör program i miljöer med ett begränsat antal anslutningar för socket. 

     När du använder SDK: n i Azure Functions, i synnerhet i [förbruknings planen](../azure-functions/functions-scale.md#consumption-plan), mindful de aktuella [gränserna i anslutningar](../azure-functions/manage-connections.md). I så fall kan gateway-läget rekommenderas om du även arbetar med andra HTTP-baserade klienter i ditt Azure Functions-program.

   * Direkt läge

     Direct-läget stöder anslutningar via TCP-protokollet och är standard anslutnings läget om du använder [Microsoft. Azure. Cosmos/.net v3 SDK](sql-api-sdk-dotnet-standard.md).

     När du använder Gateway-läge, Cosmos DB använder port 443 och portarna 10250, 10255 och 10256 när du använder Azure Cosmos DBs API för MongoDB. 10250-porten mappas till en standard-MongoDB-instans utan geo-replikering och 10255/10256-portar mappar till MongoDB-instansen med geo-replikering. När du använder TCP i direkt läge måste du förutom Gateway-portarna se till att port intervallet är mellan 10000 och 20000 är öppen eftersom Azure Cosmos DB använder dynamiska TCP-portar. Om de här portarna inte är öppna och du försöker använda TCP, får du ett fel av typen 503-tjänst ej tillgänglig. I följande tabell visas anslutnings lägen som är tillgängliga för olika API: er och användare av tjänst portar för varje API:

     |Anslutnings läge  |Protokoll som stöds  |SDK: er som stöds  |API/tjänst-port  |
     |---------|---------|---------|---------|
     |Gateway-  |   HTTPS    |  All SDKS    |   SQL (443), Mongo (10250, 10255, 10256), tabell (443), Cassandra (10350), Graf (443)    |
     |Direct    |     TCP    |  .NET SDK    | Portar inom 10 000 – 20000-intervall |

     Azure Cosmos DB erbjuder en enkel och öppen RESTful programmerings modell över HTTPS. Dessutom erbjuder den ett effektivt TCP-protokoll, som också RESTful i sin kommunikations modell och är tillgängligt via .NET-klient-SDK: n. TCP-protokollet använder SSL för inledande autentisering och kryptering av trafik. Använd TCP-protokollet när det är möjligt för bästa prestanda.

     För SDK v3 konfigureras anslutnings läget när CosmosClient-instansen skapas, som en del av CosmosClientOptions, kom ihåg att Direct-läget är standardvärdet.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
     new CosmosClientOptions
     {
        ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
     });
     ```

     För Microsoft. Azure. DocumentDB SDK konfigureras anslutnings läget under konstruktion av DocumentClient-instansen med parametern ConnectionPolicy. Om direkt läge används kan protokollet också anges i parametern ConnectionPolicy.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct, //ConnectionMode.Gateway is the default
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Eftersom TCP endast stöds i direkt läge, om Gateway-läge används, används alltid HTTPS-protokollet för att kommunicera med gatewayen och protokollets värde i ConnectionPolicy ignoreras.

     ![Bild av Azure Cosmos DB anslutnings princip](./media/performance-tips/connection-policy.png)

2. **Anropa openAsync för att undvika start fördröjning för första begäran**

    Den första begäran har som standard en högre latens eftersom den måste hämta tabellen Address routing. När du använder [SDK v2](sql-api-sdk-dotnet.md)för att undvika den här start fördröjningen för den första begäran, bör du anropa openAsync () en gång under initieringen enligt följande.

        await client.OpenAsync();

    > [!NOTE] 
    > OpenAsync-metoden genererar begär Anden för att hämta adress routningstabellen för alla behållare i kontot. För konton som har många behållare men deras program har åtkomst till en delmängd av dem, genererar den en onödig mängd trafik som gör initieringen långsam. Det kan vara bra att använda openAsync-metoden i det här scenariot eftersom det gör att program starten går långsamt.

   <a id="same-region"></a>
3. **Samordna-klienter i samma Azure-region för prestanda**

    När det är möjligt kan du placera alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos-databasen. För en ungefärlig jämförelse kan anrop till Azure Cosmos DB inom samma region slutföras inom 1-2 MS, men svars tiden mellan västra USA och östra kust är > 50 ms. Den här fördröjningen kan troligt vis variera från begäran till begäran beroende på den väg som tas av begäran när den skickas från klienten till Azure Data Center-gränser. Den lägsta möjliga fördröjningen uppnås genom att se till att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB slut punkten. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).

    ![illustration av Azure Cosmos DB anslutnings princip](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Öka antalet trådar/aktiviteter**

    Eftersom anrop till Azure Cosmos DB görs över nätverket kan du behöva variera graden av parallellitet för dina begär Anden så att klient programmet ägnar mycket lite tid åt att vänta mellan begär Anden. Om du till exempel använder. NET- [aktivitetens parallella bibliotek](https://msdn.microsoft.com//library/dd460717.aspx), skapa i ordningen 100 uppgifter som läses eller skrivs till Azure Cosmos dB.

5. **Aktivera accelererat nätverk**

   För att minska svars tid och CPU-Darr rekommenderar vi att de virtuella klient datorerna är accelererade nätverk aktiverade. Se [skapa en virtuell Windows-dator med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md) eller [skapa en virtuell Linux-dator med accelererade nätverks](../virtual-network/create-vm-accelerated-networking-cli.md) artiklar för att aktivera accelererat nätverk.


## <a name="sdk-usage"></a>SDK-användning
1. **Installera den senaste SDK: n**

    Azure Cosmos DB SDK: er har ständigt förbättrats för att ge bästa möjliga prestanda. Se de [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) -sidorna för att fastställa de senaste SDK: er och gransknings förbättringarna.

2. **Använda Stream-API: er**

    [.NET SDK v3](sql-api-sdk-dotnet-standard.md) innehåller stream-API: er som kan ta emot och returnera data utan att serialiseras. 

    Program på mellan nivå som inte konsumerar svaren från SDK: n direkt, men som vidarebefordrar dem till andra program nivåer kan dra nytta av data Ströms-API: erna. Se exempel på [objekt hanterings](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) exempel för hantering av data strömmar.

3. **Använd en singleton Azure Cosmos DB-klient för programmets livs längd**

    Varje DocumentClient-och CosmosClient-instans är tråd säker och utför effektiv anslutnings hantering och cachelagring av adresser när de körs i direkt läge. För att möjliggöra effektiv anslutnings hantering och bättre prestanda av SDK-klienten rekommenderar vi att du använder en enda instans per AppDomain för programmets livs längd.

   <a id="max-connection"></a>
4. **Öka System.Net MaxConnections per värd när du använder Gateway-läge**

    Azure Cosmos DB begär Anden görs via HTTPS/REST vid användning av Gateway-läge och omfattas av standard anslutnings gränsen per värdnamn eller IP-adress. Du kan behöva ange MaxConnections till ett högre värde (100-1000) så att klient biblioteket kan använda flera samtidiga anslutningar till Azure Cosmos DB. I .NET SDK-1.8.0 och senare är standardvärdet för [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50 och du kan ändra värdet genom att ange filen [Documents. client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) till ett högre värde.   
5. **Justera parallella frågor för partitionerade samlingar**

     SQL .NET SDK-version 1.9.0 och senare stöder parallella frågor, som gör att du kan fråga en partitionerad samling parallellt. Mer information finns i [kod exempel](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) för att arbeta med SDK: er. Parallella frågor är utformade för att förbättra svars tid och data flöde för deras serie motsvarighet. Parallella frågor ger två parametrar som användarna kan justera för att anpassa sig efter deras krav, (a) MaxDegreeOfParallelism: för att kontrol lera det högsta antalet partitioner kan frågas parallellt och (b) MaxBufferedItemCount: för att kontrol lera antalet resultat som redan hämtats.

    (a) ***Justera graden av parallellitet\:*** parallell fråga fungerar genom att fråga flera partitioner parallellt. Men data från en enskild partition hämtas seriellt i förhållande till frågan. Att ange `MaxDegreeOfParallelism` i [SDK v2](sql-api-sdk-dotnet.md) eller `MaxConcurrency` i [SDK v3](sql-api-sdk-dotnet-standard.md) till antalet partitioner har maximal chans att uppnå den mest utförda frågan, förutsatt att alla andra system villkor är oförändrade. Om du inte känner till antalet partitioner kan du ange graden av parallellitet till ett högt tal, och systemet väljer det lägsta (antal partitioner, indata från användaren) som graden av parallellitet.

    Det är viktigt att Observera att parallella frågor ger de bästa fördelarna om data är jämnt fördelade över alla partitioner med avseende på frågan. Om den partitionerade samlingen är partitionerad, så att alla eller en majoritet av de data som returneras av en fråga är koncentrerade i några partitioner (en partition i värsta fall), skulle prestandan för frågan bli Flask hals av dessa partitioner.

    (b) ***justering av MaxBufferedItemCount\:*** parallell fråga är utformad för att hämta resultat när den aktuella gruppen med resultat bearbetas av klienten. För hämtning bidrar till den totala tids fördröjnings förbättringen av en fråga. MaxBufferedItemCount är parametern för att begränsa antalet förhämtade resultat. Om du anger MaxBufferedItemCount till det förväntade antalet returnerade resultat (eller ett högre tal) kan frågan ta emot maximal nytta av för hämtning.

    För hämtning fungerar på samma sätt oavsett graden av parallellitet och det finns en enda buffert för data från alla partitioner.  
6. **Aktivera server sidans GC**

    En minskning av skräp insamlingens frekvens kan i vissa fall hjälpa dig. I .NET anger du [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) till true.
7. **Implementera backoff med RetryAfter-intervall**

    Under prestanda testningen bör du öka belastningen tills en låg frekvens av begär Anden blir begränsad. Om detta är begränsat bör klient programmet backoff vid begränsningen för det Server-angivna återförsöksintervallet. Genom att respektera backoff garanterar du att du tillbringar minimal tid på att vänta mellan återförsök. Princip support för återförsök ingår i version 1.8.0 och senare av SQL [.net](sql-api-sdk-dotnet.md) och [Java](sql-api-sdk-java.md), version 1.9.0 och senare av [Node. js](sql-api-sdk-node.md) och [python](sql-api-sdk-python.md), och alla versioner av [.net Core](sql-api-sdk-dotnet-core.md) SDK: er som stöds. Mer information finns i [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    Med version 1,19 och senare av .NET SDK finns det en mekanism för att logga ytterligare diagnostikinformation och felsöka latens problem som visas i följande exempel. Du kan logga den diagnostiska strängen för förfrågningar som har en högre Läs fördröjning. Den hämtade diagnostikloggar hjälper dig att förstå hur många gånger du har observerat 429s för en specifik begäran.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
8. **Skala ut din klient arbets belastning**

    Om du testar med höga data flödes nivåer (> 50000 RU/s) kan klient programmet bli Flask halsen på grund av att datorn capping ut på processor-eller nätverks användning. Om du når den här punkten kan du fortsätta att skicka det Azure Cosmos DB kontot ytterligare genom att skala ut dina klient program över flera servrar.
9. **Cachelagra dokument-URI: er för lägre Läs fördröjning**

    Cachelagra dokument-URI: er när det är möjligt för bästa möjliga Läs prestanda. Du måste definiera logik för att cachelagra ResourceID när du skapar resursen. ResourceID-baserade sökningar är snabbare än namnbaserade sökningar, så cachelagring av dessa värden förbättrar prestandan. 

   <a id="tune-page-size"></a>
10. **Justera sid storleken för frågor/läsa feeds för bättre prestanda**

   När du utför en Mass läsning av dokument med hjälp av funktionen för att läsa feeds (t. ex. ReadDocumentFeedAsync) eller när du utfärdar en SQL-fråga, returneras resultatet i ett segment om resultat mängden är för stor. Som standard returneras resultaten i segment om 100 objekt eller 1 MB, beroende på vilken gräns som nåtts först.

   För att minska antalet nätverks fördröjningar som krävs för att hämta alla tillämpliga resultat kan du öka sid storleken med [x-MS-Max-objekt-Count-](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) HEAD-begäran till upp till 1000. I de fall där du bara behöver visa några få resultat, till exempel om ditt användar gränssnitt eller ditt program-API bara returnerar 10 resultat en gång, kan du också minska sid storleken till 10 för att minska data flödet som används för läsningar och frågor.

   > [!NOTE] 
   > Egenskapen maxItemCount bör inte användas bara för sid brytnings ändamål. Det är den viktigaste användningen för att förbättra prestanda för frågor genom att minska det maximala antalet objekt som returneras på en enda sida.  

   Du kan också ange sid storlek med hjälp av tillgängliga Azure Cosmos DB SDK: er. Med egenskapen [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) i FeedOptions kan du ange det maximala antalet objekt som ska returneras i uppräknings åtgärden. När `maxItemCount` är inställt på-1 hittar SDK: n automatiskt det optimala värdet beroende på dokument storleken. Exempel:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   När en fråga körs skickas resulterande data i ett TCP-paket. Om du anger för lågt värde för `maxItemCount`, är antalet resor som krävs för att skicka data i TCP-paketet höga, vilket påverkar prestandan. Om du inte är säker på vilket värde som ska anges för `maxItemCount` egenskap är det bäst att ange det till-1 och låta SDK: n välja standardvärdet. 

11. **Öka antalet trådar/aktiviteter**

    Se [öka antalet trådar/aktiviteter](#increase-threads) i avsnittet nätverk.

12. **Använd 64-bitars värd bearbetning**

    SQL SDK fungerar i en 32-bitars värd process när du använder SQL SDK-version 1.11.4 och senare. Men om du använder kors partitions frågor rekommenderas 64-bitars värd bearbetning för bättre prestanda. Följande typer av program har 32-bitars värd process som standard, så för att ändra till 64-bit följer du dessa steg baserat på typen av program:

    - För körbara program kan du göra detta genom att avmarkera alternativet **hellre 32-bit** i fönstret **projekt egenskaper** på fliken **bygge** .

    - För VSTest-baserade test projekt kan du göra detta genom att välja test **Inställningar** för **test**->->**standard processor arkitektur som x64**, från meny alternativet **test på Visual Studio** .

    - För lokalt distribuerade ASP.NET-webbappar kan du göra detta genom att kontrol lera att **använda 64-bitars versionen av IIS Express för webbplatser och projekt**, under **verktyg**->**alternativ**->**projekt och lösningar**->**webb projekt**.

    - För ASP.NET webb program som distribueras i Azure kan du göra detta genom att välja **plattformen som 64-bit** i **program inställningarna** på Azure Portal.

## <a name="indexing-policy"></a>Indexeringspolicy
 
1. **Utesluta sökvägar som inte används från indexering för att få snabbare skrivning**

    Med Cosmos DB indexerings principen kan du också ange vilka dokument Sök vägar som ska tas med eller undantas från indexering genom att använda indexerings Sök vägar (IndexingPolicy. IncludedPaths och IndexingPolicy. ExcludedPaths). Användningen av indexerings Sök vägar kan ge bättre skriv prestanda och lägre index lagring för scenarier där fråge mönstren är kända i förväg, eftersom indexerings kostnader direkt korreleras med antalet unika sökvägar som indexeras.  Följande kod visar till exempel hur du undantar en hel del av dokumenten (ett under träd) från indexering med jokertecknet "*".

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Mer information finns i [Azure Cosmos DB indexerings principer](index-policy.md).

## <a name="throughput"></a>Dataflöde
<a id="measure-rus"></a>

1. **Mått och justering för lägre enheter för programbegäran/andra användning**

    Azure Cosmos DB erbjuder en omfattande uppsättning databas åtgärder, inklusive relationella och hierarkiska frågor med UDF: er, lagrade procedurer och utlösare – allt som körs på dokumenten i en databas samling. Den kostnad som hör till var och en av dessa operationer varierar baserat på vilken CPU, vilka IO-resurser och hur mycket minne som krävs för att slutföra operationen. I stället för att tänka på och hantera maskin varu resurser kan du tänka på en enhet för begäran (RU) som ett enda mått för de resurser som krävs för att utföra olika databas åtgärder och betjäna en programbegäran.

    Data flödet har allokerats baserat på antalet enheter för [programbegäran](request-units.md) som angetts för varje behållare. Den begärda enhets förbrukningen utvärderas som en taxa per sekund. Program som överskrider den allokerade enhets hastigheten för deras behållare är begränsade tills hastigheten sjunker under den allokerade nivån för behållaren. Om ditt program kräver en högre data flödes nivå kan du öka data flödet genom att tillhandahålla ytterligare enheter för programbegäran. 

    Komplexiteten i en fråga påverkar hur många enheter för programbegäran som används för en åtgärd. Antalet predikat, typen av predikat, antalet UDF: er och storleken på käll data uppsättningen påverkar kostnaden för frågor.

    Om du vill mäta omkostnaderna för en åtgärd (skapa, uppdatera eller ta bort) kan du kontrol lera huvudet [x-MS-Request-Charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (eller motsvarande RequestCharge-egenskap i ResourceResponse\<t > eller FeedResponse\<t > i .NET SDK) för att mäta antalet enheter för programbegäran som används av dessa åtgärder.

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

    Begär ande avgiften som returnerades i den här rubriken är en bråkdel av ditt etablerade data flöde (t. ex. 2000 ru: er/sekund). Om till exempel föregående fråga returnerar 1000 1 KB-dokument, är kostnaden för åtgärden 1000. I och med den här servern bevarar servern bara två sådana begär Anden innan hastigheten begränsar efterföljande begär Anden. Mer information finns i [enheter för programbegäran](request-units.md) och [Kalkylatorn för begär ande](https://www.documentdb.com/capacityplanner)enheter.
<a id="429"></a>
2. **Hastighets begränsning/begär ande frekvens för stor**

    När en klient försöker överskrida det reserverade data flödet för ett konto, finns det ingen prestanda försämring på servern och ingen användning av data flödes kapaciteten utöver den reserverade nivån. Servern kommer att förebyggande syfte avsluta begäran med RequestRateTooLarge (HTTP-statuskod 429) och returnera huvudet [x-MS-retry-efter-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) som anger hur lång tid i millisekunder som användaren måste vänta innan begäran försöker igen.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK: erna fångar alla implicita dessa svar, med den server-angivna återförsöket-efter-rubriken och gör om begäran. Om ditt konto inte kan nås samtidigt av flera klienter kommer nästa försök att lyckas.

    Om du har mer än en klient ackumulerad på ett konsekvent sätt över begär ande frekvensen är standard antalet nya försök som för närvarande är 9 internt av klienten inte tillräckligt. i det här fallet genererar klienten en DocumentClientException med status kod 429 i programmet. Standard antalet återförsök kan ändras genom att ange RetryOptions på ConnectionPolicy-instansen. Som standard returneras DocumentClientException med status kod 429 efter en ackumulerad vänte tid på 30 sekunder om begäran fortsätter att köras över begär ande frekvensen. Detta inträffar även om det aktuella antalet återförsök är mindre än max antalet försök, måste det vara standardvärdet 9 eller ett användardefinierat värde.

    Även om det automatiserade återförsöket hjälper till att förbättra återhämtning och användbarhet för de flesta program, kan det komma på strider när du gör prestanda mått, särskilt när du mäter svars tid.  Den klientbaserade svars tiden kommer att inaktive ras om experimentet träffar Server begränsningen och gör att klient-SDK: n kan försöka tyst igen. För att undvika fördröjningar vid prestanda experiment kan du mäta den avgift som returneras av varje åtgärd och se till att förfrågningarna fungerar under den reserverade begär ande frekvensen. Mer information finns i [enheter för programbegäran](request-units.md).
3. **Design för mindre dokument för högre data flöde**

    Begär ande avgiften (dvs. bearbetnings kostnad för begäran) för en specifik åtgärd är direkt korrelerad med dokumentets storlek. Åtgärder i stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg
Ett exempel program som används för att utvärdera Azure Cosmos DB för scenarier med hög prestanda på ett fåtal klient datorer finns i [prestanda-och skalnings testning med Azure Cosmos DB](performance-testing.md).

För att lära dig mer om att utforma programmet för skalning och hög prestanda, se [partitionering och skalning i Azure Cosmos DB](partition-data.md).
