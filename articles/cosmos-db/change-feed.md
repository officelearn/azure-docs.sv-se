---
title: Arbeta med ändringen feeds stöd i Azure Cosmos DB | Microsoft Docs
description: Använda Azure Cosmos DB ändra feed stöd för att spåra ändringar i dokument och utföra händelsebaserat bearbetning som utlösare och uppdatera cacheminnen och analyser system kontinuerligt.
keywords: Ändra feed
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 8475c79782730e989f9590566c31ccd50af9f144
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302054"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Arbeta med ändringen feeds stöd i Azure Cosmos DB

[Azure Cosmos-DB](../cosmos-db/introduction.md) är ett snabbt och flexibel globalt replikerad databas, väl lämpade för IoT, återförsäljnings, spel och operativa loggningsprogram. En gemensam designmönstret i dessa program är att använda ändringar av data till startar ytterligare åtgärder. Dessa ytterligare åtgärder kan vara något av följande: 

* Utlösa en avisering eller ett anrop till en API när ett dokument infogas eller ändras.
* Dataströmmen för IoT bearbetades eller utföra analyser.
* Flytt av ytterligare data genom att synkronisera med en cache, sökmotor eller datalagret eller arkivering av data till kyla.

Den **ändra feed stöd** i Azure Cosmos DB gör att du kan skapa skalbara lösningar för var och en av dessa mönster som visas i följande bild:

![Med hjälp av Azure Cosmos DB ändra feed power analys i realtid och händelsedriven datascenarier](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Ändra feed support tillhandahålls för alla datamodeller och behållare i Azure Cosmos DB. Ändra feeden läses med hjälp av SQL-klienten och Serialiserar objekt i JSON-format. På grund av formatering, JSON MongoDB klienter får ett matchningsfel mellan BSON formaterade dokument och JSON-formaterad ändra feed.

I följande videoklipp visar Azure Cosmos DB Programhanteraren Andrew Liu hur Azure Cosmos DB ändringen feed fungerar.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>Hur feed arbete av ändringen?

Ändra feed-stöd i Azure Cosmos DB fungerar genom att lyssna på en Azure DB som Cosmos-samling som eventuella ändringar. Därefter visas den sorterade listan över dokument som har ändrats i den ordning som de har ändrats. Ändringarna sparas kan bearbetas asynkront och inkrementellt och utdata kan fördelas på en eller flera konsumenter för parallell bearbetning. 

Du kan läsa ändringen feed på tre olika sätt, enligt beskrivningen nedan:

*   [Med hjälp av Azure Functions](#azure-functions)
*   [Med hjälp av Azure Cosmos DB SDK](#sql-sdk)
*   [Med hjälp av Azure Cosmos DB ändringen feed processor-bibliotek](#change-feed-processor)

Ändra feeden är tillgängligt för varje partitionsnyckelintervallet inom dokumentsamlingen och därför kan fördelas på en eller flera konsumenter för parallell bearbetning som visas i följande bild.

![Distribuerad bearbetning av Azure Cosmos DB ändra feed](./media/change-feed/changefeedvisual.png)

Ytterligare information:
* Ändra feeden är aktiverad som standard för alla konton.
* Du kan använda din [etablerat dataflöde](request-units.md) i din region skrivning eller någon [läsa region](distribute-data-globally.md) för att läsa från ändringen feed, precis som andra Azure DB som Cosmos-åtgärden.
* Ändra feeden innehåller infogningar och uppdateringsåtgärder som gjorts i dokument i samlingen. Du kan avbilda borttagningar genom att ange en ”soft-ta bort” flagga i dokument i stället för borttagningar. Alternativt kan du ställa en begränsad utgångsperioden för dina dokument via den [TTL kapaciteten](time-to-live.md), till exempel 24 timmar och Använd värdet för egenskapen att avbilda borttagningar. Med den här lösningen har att bearbeta ändringar inom ett kortare tidsintervall än förfalloperiod TTL-värde.
* Varje ändring i ett dokument visas en gång i ändringen feed och klienter hantera sina kontrollpunkter logik. Ändra feed processor biblioteket innehåller automatiska kontrollpunkter och ”minst en gång” semantik.
* Den senaste ändringen för ett visst dokument ingår i ändringsloggen. Mellanliggande ändringar kan inte användas.
* Ändra feeden sorteras ordning ändras inom varje partitionsnyckelvärde. Det finns ingen garanterad ordning över partitionsnyckel värden.
* Ändringar som kan synkroniseras från alla i tidpunkt, det är ingen fast Datalagringsperiod ändringar är tillgängliga.
* Ändringar är tillgängliga i mängder partition nyckelintervall. Den här funktionen kan ändringar från stora samlingar som kan bearbetas parallellt i flera konsumenter-servrar.
* Program kan begära flera ändra feeds samtidigt på samma samling.
* ChangeFeedOptions.StartTime kan användas för att tillhandahålla en inledande startpunkten, till exempel, för att hitta den fortsättningstoken som motsvarar den angivna tiden. ContinuationToken, wins om anges över värdena StartTime och StartFromBeginning. Precisionen för ChangeFeedOptions.StartTime är ~ 5 sekunder. 

## <a name="use-cases-and-scenarios"></a>Användningsfall och scenarier

Ändra feeden kan effektiv bearbetning av stora datamängder med en stor volym med skrivningar och ett alternativ till att fråga en hela datamängden för att identifiera vad som har ändrats. 

Till exempel med en feed, kan du utföra följande uppgifter effektivt:

* Uppdatera ett cacheminne, sökindex eller ett datalager med data som lagras i Azure Cosmos DB.
* Implementera programnivå data lagringsnivåer och arkivering, som är ”varm data” lagras i Azure Cosmos DB och föråldrade ”kalldata” för att [Azure Blob Storage](../storage/common/storage-introduction.md) eller [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Utföra noll driftstopp migrering till ett annat Azure DB som Cosmos-konto med en annan partitioneringsschema.
* Implementera [lambda pipelines på Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) med Azure Cosmos DB. Azure Cosmos-DB tillhandahåller en skalbar databaslösning som kan hantera både införandet och fråga och implementera lambda arkitekturer med låg TCO. 
* Ta emot och lagrar händelsedata från enheter, sensorer, infrastruktur och program och bearbeta händelserna i realtid med [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md), eller [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

Följande bild visar hur lambda pipelines som båda mata in och frågan med hjälp av Azure Cosmos DB kan använda ändra feed stöd: 

![Azure DB Cosmos-baserade lambda pipeline för införandet och fråga](./media/change-feed/lambda.png)

Dessutom inom din [serverlösa](http://azure.com/serverless) webb- och mobilappar kan du spåra händelser, till exempel ändringar i kundens profil, inställningar eller plats för att aktivera vissa åtgärder som att skicka push-meddelanden till sina enheter med hjälp av [Azure Functions](#azure-functions). Om du använder Azure Cosmos DB för att skapa ett spel, kan du, till exempel använda ändra feed att implementera realtid ledartavlor baserat på resultat från slutförda spel.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Med hjälp av Azure Functions 

Om du använder Azure Functions, är det enklaste sättet att ansluta till en Azure Cosmos DB ändra feed att lägga till en Azure DB som Cosmos-utlösare i appen Azure Functions. När du skapar en Azure DB som Cosmos-utlösare i en app i Azure Functions, väljer du Azure DB som Cosmos-samlingen för att ansluta till och funktionen utlöses när en ändring i samlingen. 

Utlösare kan skapas i Azure Functions-portalen i Azure DB som Cosmos-portalen eller programmässigt. Mer information finns i [Azure Cosmos DB: serverlösa databasen datoranvändning med hjälp av Azure Functions](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Med SDK

Den [SQL SDK](sql-api-sdk-dotnet.md) för Azure Cosmos DB kan du alla kan läsa och hantera en ändring feed. Men med bra power kommer många ansvarsområden för. Om du vill hantera kontrollpunkter, hantera dokument sekvensnummer och detaljerad kontroll över partitionsnycklar kan med SDK vara rätt metod.

Det här avsnittet går igenom hur du använder SQL-SDK ska fungera med en feed.

1. Börja med att läsa följande resurser från appconfig. Instruktioner om hur du hämtar nyckeln slutpunkt och auktorisering finns i [uppdatera anslutningssträngen](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Skapa klienten enligt följande:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Hämta partitionen viktiga områden:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Anropa ExecuteNextAsync för varje partitionsnyckelintervallet:

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

> [!NOTE]
> I stället för `ChangeFeedOptions.PartitionKeyRangeId`, kan du använda `ChangeFeedOptions.PartitionKey` att ange en enskild partitionsnyckel som du vill hämta en ändring feed. Till exempel `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Om du har flera läsare, kan du använda **ChangeFeedOptions** att distribuera skrivskyddade inläsning till olika trådar eller olika klienter.

Och som är det, med dessa några rader kod kan du börja läsa ändra feeden. Du kan hämta den fullständiga koden som används i denna artikel från den [GitHub-repo-](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

I koden i steg 4 ovan, den **ResponseContinuation** under senaste raden innehåller den sista logiska sekvensnumret (LSN) av dokument som du ska använda nästa gång du läsa nya dokument efter den här sekvensnummer. Med hjälp av den **StartTime** av den **ChangeFeedOption** utvidga din net för att hämta dokumenten. I så fall om din **ResponseContinuation** är null, men din **StartTime** går tillbaka i tiden och sedan får du alla dokument som ändrats sedan den **StartTime**. Men om din **ResponseContinuation** har ett värde system får du alla dokument eftersom den LSN.

Så hindrar kontrollpunkt matrisen bara LSN för varje partition. Men om du inte vill hantera partitionerna kontrollpunkter, LSN, starttid, etc. enklare alternativet är att använda ändringen feed processor-biblioteket.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Med hjälp av ändringen feed processor-bibliotek 

Den [Azure Cosmos DB ändra feed processor biblioteket](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) kan hjälpa dig att enkelt distribuera händelsebearbetning mellan flera användare. Det här biblioteket förenklar läsning ändringar i partitioner och flera trådar som arbetar parallellt.

Den största fördelen av ändra feed processor-biblioteket är att du inte behöver hantera varje partition och fortsättningstoken och du behöver inte söka varje samling manuellt.

Ändra feed processor bibliotek förenklar läsning ändringar i partitioner och flera trådar som arbetar parallellt.  Den hanterar automatiskt läsning ändringar över partitioner som använder en mekanism för lånet. Som du ser i följande bild, om du startar två klienter som använder ändringen feed processor bibliotek, delar de arbete sinsemellan. När du fortsätter att öka klienterna behålla de dividera arbete sinsemellan.

![Distribuerad bearbetning av Azure Cosmos DB ändra feed](./media/change-feed/change-feed-output.png)

Vänster klienten startades första och den startade övervakningen alla partitioner och andra klienten startades och sedan först släpper vissa lån till andra klienten. Du kan se det här är bra sätt att distribuera arbete mellan olika datorer och klienter.

Observera att om du har två serverlösa Azure funktioner som krävs för övervakning i samma samling och använder samma lånet och sedan de två funktionerna kan få olika dokument, beroende på hur processor-biblioteket beslutar att åtgärd partitionerna.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Förstå ändringen feed processor-bibliotek

Det finns fyra huvudsakliga komponenter för att implementera ändra feed processor biblioteket: samlingen övervakade, lån samlingen, värd för händelsebearbetning och konsumenterna. 

> [!WARNING]
> Det finns priseffekter med att skapa en samling eftersom du reserverar genomströmning för programmet för att kommunicera med Azure Cosmos DB. Mer information finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Insamling av övervakade:** övervakade samlingen är data som ändras feeden genereras. Alla infogningar och ändringar i övervakade samlingen återspeglas i feeden ändring i mängden. 

**Insamling av lånet:** lån samling koordinaterna ändringen feed över flera arbetare bearbetas. En separat samling används för att lagra lån med en leasing per partition. Är det fördelaktigt för att lagra samlingen lån på ett annat konto med området skrivåtgärder närmare till där ändringen feed processor körs. Ett lån-objekt innehåller följande attribut: 
* Ägare: Anger den värd som äger lånet
* Fortsättning: Anger placeringen (fortsättningstoken) i ändra flödet för en viss partition
* Tidsstämpel: Tid för senaste lån uppdaterades; tidsstämpeln kan användas för att kontrollera om lånet betraktas som upphört att gälla 

**Värd för händelsebearbetning:** varje värd anger hur många partitioner att bearbeta baserat på hur många andra instanser av värdar har aktiva lån. 
1.  När en värd startas, skaffar lån för att utjämna belastningen över alla värdar. En värd förnyas regelbundet lån, så att förbli lån. 
2.  En värd kontrollpunkter senaste fortsättningstoken till lånet för varje läsa. För att säkerställa säkerheten för samtidighet, kontrollerar en värd ETag för varje lease-uppdatering. Andra kontrollpunkt strategier stöds också.  
3.  En värd släpper alla lån vid avstängning men behålls fortsättning-information så att den kan återupptas senare läsning från den lagrade kontrollpunkten. 

Antalet värdar kan inte vara större än antalet partitioner (lån) just nu.

**Konsumenterna:** konsumenter eller arbetare, är trådar som behandlar ändra feed initieras av varje värd. Varje värd för händelsebearbetning kan ha flera konsumenter. Varje konsument läser ändringen feed från partitionen tilldelas och meddela dess värden för ändringar och gått lån.

För att bättre förstå hur dessa fyra element i ändra feed processor arbete tillsammans ska vi titta på ett exempel i följande diagram. Samlingen övervakade lagrar dokument och använder ”stad” som partitionsnyckel. Vi kan se att den blå partitionen innehåller dokument med fältet ”Ort” från ”A E” och så vidare. Det finns två värdar med två konsumenter läsning från de fyra partitionerna parallellt. Pilarna visar konsumenterna läsning från en specifik plats i ändringen feed. I den första partitionen representerar mörkare blå oläst ändringar medan lätta blå representerar redan skrivskyddade ändringarna på ändringen feed. Värdarna använda lån-samlingen för att lagra ett värde för ”fortsättning” för att hålla reda på den aktuella positionen för läsning för varje konsument. 

![Med hjälp av Azure Cosmos DB ändringen feed värd för händelsebearbetning](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Arbeta med ändringen feed processor-bibliotek

Innan du installerar ändra feed processor NuGet-paketet först installera: 

* Microsoft.Azure.DocumentDB senaste versionen.
* Newtonsoft.Json, senaste versionen

Installera sedan den [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) och inkludera den som en referens.

Att genomföra ändringen feed processor biblioteket som du behöver göra följande:

1. Implementera en **DocumentFeedObserver** -objekt som implementerar **IChangeFeedObserver**.
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. Implementera en **DocumentFeedObserverFactory**, som implementerar en **IChangeFeedObserverFactory**.
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. Definiera *CancellationTokenSource* och *ChangeFeedProcessorBuilder*

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. skapa den **ChangeFeedProcessorBuilder** när du har definierat relevanta objekt 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
            ```

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>Kan jag läsa ändra feed använder Java?

Java-bibliotek för att läsa ändra feed är tillgänglig i [Github-lagringsplatsen](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). För närvarande finns Java-bibliotek dock några versioner bakom .NET-biblioteket. Båda biblioteken kommer snart att synkroniserade.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Kan jag använda _etag, _lsn eller _ts för interna bokföring som visas i svaret?

_etag format är intern och du bör inte beroende av den (inte att parsa den) eftersom den kan ändras när som helst.
_ts är ändring eller skapa en tidsstämpel. Du kan använda _ts kronologisk jämförelse.
_lsn är är en batch-id som läggs endast för ändra feed, representerar transaktions-id från arkivet... Många dokument kan ha samma _lsn.
En sak att notera ETag på FeedResponse skiljer sig från _etag som du ser på dokumentet. _etag är en intern identifierare och används för att samtidighet, ta reda på om versionen av dokumentet och ETag för ordningsföljd feeden.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Lägger du till eventuella ytterligare kostnader genom att läsa ändra feed?

Du debiteras för RU används i d.v.s. data flyttas till och från Azure Cosmos DB samlingar alltid använda RU. Användare kommer att debiteras för RU som används av samlingen lån.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Flera Azure Functions kan läsa en samling ändra feed?

Ja. Flera Azure Functions kan läsa samma samling ändra feed. Azure Functions måste dock ha en separat leaseCollectionPrefix som definierats.

### <a name="should-the-lease-collection-be-partitioned"></a>Vill du partitionerade lån samlingen?

Nej, lån samling kan åtgärdas. Partitionerade lån samling behövs inte och stöds inte för närvarande.

### <a name="can-i-read-change-feed-from-spark"></a>Kan jag läsa ändra feed från Spark?

Ja, det kan du. Se [Azure Cosmos DB Spark Connector](spark-connector.md). Här är en [skärmen omvandlingen](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) visar hur du kan bearbeta ändring feed som en strukturerad dataström.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Om det bearbetar ändra feed med hjälp av Azure Functions, säg en batch med 10 dokument och ett felmeddelande visas vid 7 dokumentet. I så fall bearbetas de tre sista dokument inte hur kan jag börja bearbeta från det misslyckade dokumentet (dvs 7 dokument) i min nästa feed?

Om du vill hantera felet är rekommenderade mönster du omsluter din kod med försök catch-block. Catch-felet och placera dokumentet i kön (obeställbara) och definierar logik för att hantera dokument som fel. Med den här metoden om du har en 200-dokumentet batch och endast ett av dokumenten misslyckades, behöver du inte kasta bort hela gruppen.

Om felet inte ska du spola check point tillbaka till början kommer annan du kan får dessa dokument från ändra feed. Kom ihåg att ändra feed behåller den senaste slutliga snapin som visar dokument på grund av det du kan förlora den tidigare ögonblicksbilden på dokumentet. Ändra feed håller bara en senaste versionen av dokumentet och mellan andra processer kan komma och ändra dokumentet.

Som du hålla korrigerat koden, hittar du snart inga dokument på obeställbara meddelanden.
Azure Functions anropas automatiskt genom att ändra feed system och kontrollera punkt osv hanteras internt av Azure-funktion. Om du vill återställa Kontrollera platsen och styra alla aspekter av det, bör du använda ändra feed Processor SDK.


## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure Cosmos DB med Azure Functions finns [Azure Cosmos DB: serverlösa databasen datoranvändning med hjälp av Azure Functions](serverless-computing-database.md).

Mer information om hur du använder ändringen feed processor biblioteket använder du följande resurser:

* [Informationssidan](sql-api-sdk-dotnet-changefeed.md) 
* [Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Exempelkod som visar steg 1 – 6 ovan](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Ytterligare exempel på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Mer information om hur du använder ändringen feed via SDK använder du följande resurser:

* [Informationssidan om SDK](sql-api-sdk-dotnet.md)
