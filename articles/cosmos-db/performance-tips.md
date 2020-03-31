---
title: Prestandatips för Azure Cosmos DB för .NET
description: Lär dig klientkonfigurationsalternativ för att förbättra Azure Cosmos DB-prestanda.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: 68a9a7d5f90831bb2e0c9284cb71ae4b92edffad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131412"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Prestandatips för Azure Cosmos DB och .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB är en snabb och flexibel distribuerad databas som skalas sömlöst med garanterad svarstid och dataflöde. Du behöver inte göra större arkitekturändringar eller skriva komplex kod för att skala databasen med Azure Cosmos DB. Att skala upp och ned är lika enkelt som att ringa ett enda API-anrop. Mer information finns i [hur du etablerar behållardataflöde](how-to-provision-container-throughput.md) eller [hur du etablerar databasdataflöde](how-to-provision-database-throughput.md). Men eftersom Azure Cosmos DB nås via nätverkssamtal finns det optimering på klientsidan som du kan göra för att uppnå toppprestanda när du använder [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Så, om du försöker förbättra databasens prestanda, överväga följande alternativ:

## <a name="hosting-recommendations"></a>Hosting rekommendationer

**Om du vill använda frågeintensiva arbetsbelastningar använder du Windows 64-bitars i stället för värdbearbetning av Linux eller Windows 32-bitars**

Vi rekommenderar windows 64-bitars värdbearbetning för bättre prestanda. SQL SDK innehåller en inbyggd ServiceInterop.dll för att tolka och optimera frågor lokalt. ServiceInterop.dll stöds endast på Windows x64-plattformen. För Linux och andra plattformar som inte stöds där ServiceInterop.dll inte är tillgängligt görs ytterligare ett nätverksanrop till gatewayen för att få den optimerade frågan. Följande typer av program använder 32-bitars värdbearbetning som standard. Så här ändrar du värdbearbetning till 64-bitarsbearbetning, baserat på typen av program:

- För körbara program kan du ändra värdbearbetning genom att ange [plattformsmålet](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) till **x64** i fönstret **Projektegenskaper** på fliken **Skapa.**

- För VSTest-baserade testprojekt kan du ändra värdbearbetning genom att välja**Standardprocessorarkitektur för** **testinställningar** > **Test Settings** > som X64 på Visual Studio **Test-menyn.**

- För lokalt distribuerade ASP.NET webbprogram kan du ändra värdbearbetning genom att välja **Använd 64-bitarsversionen av IIS Express för webbplatser och projekt** under **Verktygsalternativ** > **Options** > **Projekt och lösningar** > **Webbprojekt**.

- För ASP.NET webbprogram som distribueras på Azure kan du ändra värdbearbetning genom att välja **64-bitarsplattformen** i **programinställningar i** Azure-portalen.

> [!NOTE] 
> Som standard är nya Visual Studio-projekt **inställda på Valfri CPU**. Vi rekommenderar att du ställer in projektet på **x64** så att det inte växlar till **x86**. Ett projekt inställt på **Valfri CPU** kan enkelt växla till **x86** om ett x86-beroende läggs till.<br/>
> ServiceInterop.dll måste finnas i mappen som SDK DLL körs från. Detta bör endast vara ett problem om du manuellt kopierar DLL:er eller har anpassade bygg-/distributionssystem.
    
**Aktivera skräpinsamling på serversidan (GC)**

Minska frekvensen av skräpinsamling kan hjälpa i vissa fall. I .NET ställer du `true` [in gcServer](https://msdn.microsoft.com/library/ms229357.aspx) på .

**Skala ut klientarbetsbelastningen**

Om du testar på höga dataflödesnivåer (mer än 50 000 RU/s) kan klientprogrammet bli flaskhalsen på grund av att datorn begränsar CPU- eller nätverksanvändningen. Om du når den här punkten kan du fortsätta att driva Azure Cosmos DB-kontot vidare genom att skala ut dina klientprogram över flera servrar.

> [!NOTE] 
> Hög CPU-användning kan orsaka ökad latens och begäran timeout undantag.

## <a name="networking"></a>Nätverk
<a id="direct-connection"></a>

**Anslutningsprincip: Använd direktanslutningsläge**

Hur en klient ansluter till Azure Cosmos DB har viktiga prestandakonsekvenser, särskilt för observerade svarstider på klientsidan. Det finns två viktiga konfigurationsinställningar för att konfigurera klientanslutningsprincipen: *anslutningsläget* och *anslutningsprotokollet*.  De två tillgängliga lägena är:

   * Gateway-läge
      
     Gateway-läge stöds på alla SDK-plattformar och är den konfigurerade standardinställningen för [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md). Om ditt program körs i ett företagsnätverk med strikta brandväggsbegränsningar är gateway-läge det bästa valet eftersom det använder standard-HTTPS-porten och en enda slutpunkt. Prestanda kompromissen är dock att gateway-läge innebär ytterligare ett nätverkshopp varje gång data läss från eller skrivs till Azure Cosmos DB. Så direktläge ger bättre prestanda eftersom det finns färre nätverkshopp. Vi rekommenderar också gatewayanslutningsläge när du kör program i miljöer som har ett begränsat antal socketanslutningar.

     När du använder SDK i Azure Functions, särskilt i [förbrukningsplanen,](../azure-functions/functions-scale.md#consumption-plan)bör du vara medveten om de aktuella [gränserna för anslutningar](../azure-functions/manage-connections.md). I så fall kan gateway-läge vara bättre om du också arbetar med andra HTTP-baserade klienter i ditt Azure Functions-program.

   * Direktläge

     Direktläge stöder anslutning via TCP-protokollet och är standardanslutningsläget om du använder [Microsoft.Azure.Cosmos/.NET V3 SDK](sql-api-sdk-dotnet-standard.md).

I gateway-läge använder Azure Cosmos DB port 443 och portar 10250, 10255 och 10256 när du använder Azure Cosmos DB API för MongoDB. Port 10250 mappar till en MongoDB-standardinstans utan georeplikering. Portarna 10255 och 10256 mappas till MongoDB-instansen som har geo-replikering.
     
När du använder TCP i direktläge, förutom gatewayportarna, måste du se till att portintervallet mellan 10000 och 20000 är öppet eftersom Azure Cosmos DB använder dynamiska TCP-portar. Om dessa portar inte är öppna och du försöker använda TCP visas ett fel på 503-tjänsten som inte är tillgänglig. Den här tabellen visar de anslutningslägen som är tillgängliga för olika API:er och de tjänstportar som används för varje API:

|Anslutningsläge  |Protokoll som stöds  |SDK:er som stöds  |API/tjänstport  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Alla SDK:er    |   SQL (443), MongoDB (10250, 10255, 10256), Tabell (443), Cassandra (10350), Diagram (443)    |
|Direct    |     TCP    |  .NET SDK    | Hamnar i intervallet 10000 till 20000 |

Azure Cosmos DB erbjuder en enkel, öppen RESTful programmeringsmodell över HTTPS. Dessutom erbjuder det ett effektivt TCP-protokoll, som också är RESTful i sin kommunikationsmodell och är tillgängligt via .NET-klienten SDK. TCP-protokollet använder SSL för inledande autentisering och kryptering av trafik. För bästa prestanda, använd TCP-protokollet när det är möjligt.

För SDK V3 konfigurerar du anslutningsläget när du skapar instansen `CosmosClient` i `CosmosClientOptions`. Kom ihåg att direktläge är standard.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

För Microsoft.Azure.DocumentDB SDK konfigurerar du anslutningsläget `DocumentClient` under konstruktionen `ConnectionPolicy` av instansen med hjälp av parametern. Om du använder direktläge kan `Protocol` du också `ConnectionPolicy` ställa in parametern med hjälp av parametern.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Eftersom TCP endast stöds i direktläge, om du använder gateway-läge, används HTTPS-protokollet alltid för att kommunicera med gatewayen `Protocol` och värdet i `ConnectionPolicy` ignoreras.

![Azure Cosmos DB-anslutningsprincipen](./media/performance-tips/connection-policy.png)

**Anropa OpenAsync för att undvika startsvarstid vid första begäran**

Som standard har den första begäran högre svarstid eftersom den måste hämta adressroutningstabellen. När du använder [SDK V2](sql-api-sdk-dotnet.md)anropar du `OpenAsync()` en gång under initieringen för att undvika den här startsvarstiden på den första begäran:

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`genererar begäranden om att hämta adressroutningstabellen för alla behållare i kontot. För konton som har många behållare men vars `OpenAsync` program kommer åt en delmängd av dem, skulle generera en onödig mängd trafik, vilket skulle göra initieringen långsam. Så `OpenAsync` att använda kanske inte är användbart i det här scenariot eftersom det saktar ner programstart.

   <a id="same-region"></a>
**För prestanda samlokalisering av klienter i samma Azure-region**

När det är möjligt placerar du alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos DB-databasen. Här är en ungefärlig jämförelse: anrop till Azure Cosmos DB inom samma region som slutförs inom 1 ms till 2 ms, men svarstiden mellan USA:s väst- och östkust är mer än 50 ms. Den här svarstiden kan variera från begäran till begäran, beroende på vilken väg som hämtas av begäran när den skickas från klienten till Azure-datacentergränsen. Du kan få lägsta möjliga svarstid genom att se till att det anropande programmet finns inom samma Azure-region som den etablerade Azure Cosmos DB-slutpunkten. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).

![Azure Cosmos DB-anslutningsprincipen](./media/performance-tips/same-region.png)<a id="increase-threads"></a>

**Öka antalet trådar/uppgifter**

Eftersom anrop till Azure Cosmos DB görs över nätverket kan du behöva variera graden av parallellitet för dina begäranden så att klientprogrammet lägger minimal tid på att vänta mellan begäranden. Om du till exempel använder .NET [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx)kan du skapa hundratals uppgifter som läs från eller skrivit till Azure Cosmos DB.

**Aktivera accelererade nätverk**
 
 För att minska svarstiden och CPU-jitter rekommenderar vi att du aktiverar snabbare nätverk på virtuella klientdatorer. Se [Skapa en virtuell Windows-dator med accelererade nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md) eller Skapa en virtuell [Linux-dator med accelererade nätverk](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK-användning
**Installera den senaste SDK**

Azure Cosmos DB SDK:er förbättras ständigt för att ge bästa prestanda. Se [Azure Cosmos DB SDK-sidor](sql-api-sdk-dotnet-standard.md) för att fastställa de senaste SDK- och granskningsförbättringarna.

**Använda strömma API:er**

[.NET SDK V3](sql-api-sdk-dotnet-standard.md) innehåller strömma API:er som kan ta emot och returnera data utan serialisering. 

Mellannivåprogram som inte använder svar direkt från SDK men vidarebefordrar dem till andra programnivåer kan dra nytta av strömma API:erna. Se [artikelhanteringsexempel](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) för exempel på strömhantering.

**Använda en Azure Cosmos DB-klient för singleton-azure cosmos för ditt programs livstid**

Varje `DocumentClient` `CosmosClient` instans och instans är gängsäker och utför effektiv anslutningshantering och adresscachelagring när du arbetar i direktläge. För att möjliggöra effektiv anslutningshantering och bättre SDK-klientprestanda `AppDomain` rekommenderar vi att du använder en enda instans per under programmets livstid.

   <a id="max-connection"></a>

**Öka System.Net MaxConnections per värd när du använder gateway-läge**

Azure Cosmos DB-begäranden görs via HTTPS/REST när du använder gateway-läge. De omfattas av standardanslutningsgränsen per värdnamn eller IP-adress. Du kan behöva `MaxConnections` ange ett högre värde (100 till 1 000) så att klientbiblioteket kan använda flera samtidiga anslutningar till Azure Cosmos DB. I .NET SDK 1.8.0 och senare är standardvärdet för [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50. Om du vill ändra värdet kan du ange [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) till ett högre värde.

**Justera parallella frågor för partitionerade samlingar**

SQL .NET SDK 1.9.0 och senare stöder parallella frågor, vilket gör att du kan fråga en partitionerad samling parallellt. Mer information finns i [kodexempel](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) relaterade till att arbeta med SDK:erna. Parallella frågor är utformade för att ge bättre frågefördröjning och dataflöde än deras seriella motsvarighet. Parallella frågor innehåller två parametrar som du kan ställa in för att passa dina behov: 
- `MaxDegreeOfParallelism`styr det maximala antalet partitioner som kan efterfrågas parallellt. 
- `MaxBufferedItemCount`kontrollerar antalet förvärmade resultat.

***Tuning grad av parallellism***

Parallellfråga fungerar genom att fråga flera partitioner parallellt. Men data från en enskild partition hämtas seriellt med avseende på frågan. Om `MaxDegreeOfParallelism` du ställer in `MaxConcurrency` i [SDK V2](sql-api-sdk-dotnet.md) eller [i SDK V3](sql-api-sdk-dotnet-standard.md) på antalet partitioner har du störst chans att uppnå den mest högpresterande frågan, förutsatt att alla andra systemvillkor förblir desamma. Om du inte vet hur många partitioner, kan du ställa in graden av parallellism till ett högt antal. Systemet kommer att välja det minsta (antal partitioner, användarindata) som graden av parallellism.

Observera att parallella frågor ger mest nytta om data är jämnt fördelade över alla partitioner med avseende på frågan. Om den partitionerade samlingen partitioneras så att alla eller de flesta av de data som returneras av en fråga är koncentrerade till några partitioner (en partition är det värsta fallet), kommer dessa partitioner flaskhals prestanda för frågan.

***Justera MaxBufferedItemCount***
    
Parallellfråga är utformad för att förhämtningsresultat medan den aktuella batchen med resultat bearbetas av klienten. Den här förhämtningen bidrar till att förbättra den övergripande svarstiden för en fråga. Parametern `MaxBufferedItemCount` begränsar antalet förhämtningsresultat. Ange `MaxBufferedItemCount` det förväntade antalet returnerade resultat (eller ett högre tal) så att frågan kan få maximal nytta av förhämtning.

Förhämtning fungerar på samma sätt oavsett graden av parallellism, och det finns en enda buffert för data från alla partitioner.  

**Implementera backoff vid återförsökEfter intervall**

Under prestandatestning bör du öka belastningen tills en liten mängd begäranden begränsas. Om begäranden begränsas bör klientprogrammet backa på gasreglaget för det serverspecificerade återförsöksintervallet. Med respekt för backoff säkerställer att du tillbringar en minimal tid väntar mellan försök. 

Principstöd för återförsök ingår i dessa SDK:er:
- Version 1.8.0 och senare av [.NET SDK för SQL](sql-api-sdk-dotnet.md) och [Java SDK för SQL](sql-api-sdk-java.md)
- Version 1.9.0 och senare av [Node.js SDK för SQL](sql-api-sdk-node.md) och [Python SDK för SQL](sql-api-sdk-python.md)
- Alla versioner av [.NET Core](sql-api-sdk-dotnet-core.md) SDK:er som stöds 

Mer information finns i [Försök igenEfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
I version 1.19 och senare av .NET SDK finns det en mekanism för loggning av ytterligare diagnostikinformation och felsökningsproblem, vilket visas i följande exempel. Du kan logga diagnostiksträngen för begäranden som har en högre lässvarstid. Den infångade diagnostiksträngen hjälper dig att förstå hur många gånger du har fått 429 fel för en viss begäran.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Uri:er för cachedokument för lägre lässvarstid**

Cachedokument-URI:er när det är möjligt för bästa läsprestanda. Du måste definiera logik för att cachelagra resurs-ID när du skapar en resurs. Uppslag baserat på resurs-ID är snabbare än namnbaserade sökningar, så cachelagring av dessa värden förbättrar prestanda.

   <a id="tune-page-size"></a>
**Justera sidstorleken för frågor/läsflöden för bättre prestanda**

När du läser massdokument med läsflödesfunktioner (till `ReadDocumentFeedAsync`exempel ) eller när du utfärdar en SQL-fråga returneras resultaten på ett segmenterat sätt om resultatuppsättningen är för stor. Som standard returneras resultaten i segment med 100 objekt eller 1 MB, beroende på vilken gräns som uppnås först.

Om du vill minska antalet nätverksrundningsturer som krävs för att hämta alla tillämpliga resultat kan du öka sidstorleken genom att använda [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) för att begära så många som 1 000 rubriker. När du bara behöver visa några få resultat, till exempel om användargränssnittet eller program-API:et bara returnerar 10 resultat åt gången, kan du också minska sidstorleken till 10 för att minska dataflödet som förbrukas för läsningar och frågor.

> [!NOTE] 
> Boendet `maxItemCount` ska inte användas bara för sidnumrering. Dess huvudsakliga användning är att förbättra prestanda för frågor genom att minska det maximala antalet objekt som returneras på en enda sida.  

Du kan också ange sidstorleken med hjälp av tillgängliga Azure Cosmos DB SDK:er. Med egenskapen [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) kan `FeedOptions` du ange det maximala antalet artiklar som ska returneras i uppräkningsåtgärden. När `maxItemCount` är inställt på -1 hittar SDK automatiskt det optimala värdet, beroende på dokumentstorleken. Ett exempel:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
När en fråga körs skickas de resulterande data i ett TCP-paket. Om du anger ett `maxItemCount`för lågt värde för är antalet resor som krävs för att skicka data i TCP-paketet högt, vilket påverkar prestanda. Så om du inte är säker på `maxItemCount` vilket värde som ska anges för egenskapen är det bäst att ställa in den på -1 och låta SDK välja standardvärdet.

**Öka antalet trådar/uppgifter**

Se [Öka antalet trådar/uppgifter](#increase-threads) i avsnittet Nätverk i den här artikeln.

## <a name="indexing-policy"></a>Indexeringspolicy
 
**Utesluta sökvägar som inte används från indexering för att få snabbare skrivning**

Med Azure Cosmos DB-indexeringsprincipen kan du också ange vilka dokumentsökvägar som ska inkluderas i eller uteslutas från indexering med hjälp av indexeringssökvägar (IndexingPolicy.IncludedPaths och IndexingPolicy.ExcludedPaths). Indexeringsvägar kan förbättra skrivprestanda och minska indexlagringen för scenarier där frågemönstren är kända i förväg. Detta beror på att indexeringskostnaderna korrelerar direkt till antalet unika sökvägar som indexeras. Den här koden visar till exempel hur du utesluter ett helt avsnitt i dokumenten (ett underträd) från indexeringen med hjälp av jokertecknet "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

Mer information finns i [Azure Cosmos DB-indexeringsprinciper](index-policy.md).

## <a name="throughput"></a>Dataflöde
<a id="measure-rus"></a>

**Mät och justera för lägre begäranheter/andra användning**

Azure Cosmos DB erbjuder en omfattande uppsättning databasåtgärder. Dessa åtgärder omfattar relations- och hierarkiska frågor med UDF:er, lagrade procedurer och utlösare, som alla fungerar i dokumenten i en databassamling. Kostnaden för var och en av dessa åtgärder varierar beroende på vilken PROCESSOR, I/O och det minne som krävs för att slutföra åtgärden. I stället för att tänka på och hantera maskinvaruresurser kan du tänka dig en begäransenhet (RU) som ett enda mått för de resurser som krävs för att utföra olika databasåtgärder och serva en programbegäran.

Dataflöde etableras baserat på antalet [begärandeenheter](request-units.md) som angetts för varje behållare. Förbrukning av begäranden enhet utvärderas som en hastighet per sekund. Program som överskrider den etablerade begäranhetssatsen för sin behållare är begränsade tills hastigheten sjunker under den etablerade nivån för behållaren. Om ditt program kräver en högre nivå av dataflöde kan du öka dataflödet genom att etablera ytterligare begärandeenheter.

Komplexiteten i en fråga påverkar hur många begärandeenheter som förbrukas för en åtgärd. Antalet predikat, typ av predikat, antalet UDF:er och storleken på källdatauppsättningen påverkar alla kostnaden för frågeåtgärder.

Om du vill mäta omkostnaderna för en åtgärd (skapa, uppdatera eller ta bort) inspekterar `FeedResponse\<T>` du [x-ms-begäran-avgiftshuvudet](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (eller motsvarande `RequestCharge` egenskap i `ResourceResponse\<T>` eller i .NET SDK) för att mäta antalet begärandeenheter som förbrukas av operationerna:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Begärandeavgiften som returneras i det här huvudet är en bråkdel av det etablerade dataflödet (det vill än 2 000 ru: er / sekund). Om till exempel föregående fråga returnerar 1 000 1 KB-dokument är kostnaden för operationen 1 000. Så, inom en sekund, servern hedrar bara två sådana förfrågningar innan kurs begränsa senare begäranden. Mer information finns i [Begäranheter](request-units.md) och [räknaren för begäranhet](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Hantera hastighetsbegränsning/begäranden för stor**

När en klient försöker överskrida det reserverade dataflödet för ett konto finns det ingen prestandaförsämring på servern och ingen användning av dataflödeskapacitet utöver den reserverade nivån. Servern avslutar begäran i förebyggande syfte med RequestRateTooLarge (HTTP-statuskod 429). Det returnerar ett [x-ms-retry-after-ms-huvud](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) som anger hur lång tid, i millisekunder, som användaren måste vänta innan han försöker göra begäran igen.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Alla SDK:er fångar underförstått det här svaret, respekterar det serverinderade återförsöks-efter-huvudet och försöker igen med begäran. Om inte ditt konto används samtidigt av flera klienter, kommer nästa återförsök att lyckas.

Om du har mer än en klient som är kumulativt konsekvent arbetar över begäranden, kanske standardantalet för återförsök för närvarande är inställt på 9 internt av klienten inte tillräckligt. I det här fallet genererar klienten en DocumentClientException med statuskod 429 till programmet. 

Du kan ändra standardantalet för `RetryOptions` återförsök genom att ange på instansen. `ConnectionPolicy` Som standard returneras DocumentClientException med statuskod 429 efter en sammanlagd väntetid på 30 sekunder om begäran fortsätter att fungera över begäranden. Det här felet returneras även när det aktuella antalet försök på nytt är mindre än det maximala antalet försök, oavsett om det aktuella värdet är standardvärdet 9 eller ett användardefinierat värde.

Det automatiska återförsöksbeteendet bidrar till att förbättra återhämtningen och användbarheten för de flesta program. Men det kanske inte är det bästa beteendet när du gör prestandariktmärken, särskilt när du mäter svarstid. Den klient observerade svarstiden kommer att öka om experimentet träffar serverbegränsningen och gör att klienten SDK tyst försöker igen. För att undvika fördröjningsstopp under prestandaexperiment mäter du debiteringen som returneras av varje åtgärd och säkerställer att begäranden fungerar under den reserverade begäranden. Mer information finns i [Begäranheter](request-units.md).

**För högre dataflöde, design för mindre dokument**

Begärsavgiften (det vill kostnaden för behandling av begäran) för en viss åtgärd korrelerar direkt till dokumentets storlek. Åtgärder på stora dokument kostar mer än åtgärder på små dokument.

## <a name="next-steps"></a>Nästa steg
Ett exempelprogram som används för att utvärdera Azure Cosmos DB för högpresterande scenarier på några klientdatorer finns i [Prestanda- och skalningstestning med Azure Cosmos DB](performance-testing.md).

Mer information om hur du utformar ditt program för skalning och hög prestanda finns [i Partitionering och skalning i Azure Cosmos DB](partition-data.md).
