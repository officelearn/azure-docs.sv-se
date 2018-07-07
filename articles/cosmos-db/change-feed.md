---
title: Arbeta med ändringen feed support i Azure Cosmos DB | Microsoft Docs
description: Använd feed support för Azure Cosmos DB-ändring för att spåra ändringar i dokument och utföra händelsebaserade bearbetning som utlösare och uppdatera cacheminnen- och analyssystem kontinuerligt.
keywords: Ändra flöde
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: e53f1e62b9265d2eec2f49537cc05c865e1436f3
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902970"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Arbeta med stöd för ändringsflödet i Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/introduction.md) är en snabb och flexibel globalt replikerade passar bra för IoT, spel, återförsäljnings, databasen och operativa loggningsprogram. Ett vanliga designmönster i dessa program är att använda ändringar av data för att sätta igång ytterligare åtgärder. Dessa ytterligare åtgärder kan vara något av följande: 

* Utlösa en avisering eller ett anrop till ett API när ett dokument infogas eller ändras.
* Stream bearbetning för IoT eller analyser.
* Ytterligare dataförflyttning genom att synkronisera med en cache, en sökmotor eller ett datalager eller arkivering av data till kall lagring.

Den **stöd för ändringsfeed** i Azure Cosmos DB gör att du kan bygga skalbara lösningar för var och en av dessa mönster som du ser i följande bild:

![Med hjälp av Azure Cosmos DB ändringen feed power realtidsanalys och händelsedrivna scenarier för högprestandaberäkning](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Ändringsfeed support tillhandahålls för alla datamodeller och behållare i Azure Cosmos DB. Ändringsflöde läses med hjälp av SQL-klienten och Serialiserar objekt till JSON-format. På grund av formatering, JSON MongoDB klienter får ett matchningsfel mellan BSON formaterade dokument och JSON-formaterad ändringsflödet.

I följande videoklipp visar Azure Cosmos DB-Programhanteraren Andrew Liu hur Azure Cosmos DB ändringsfeed fungerar.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>Hur ändringsfeed arbete?

Stöd för ändringsfeed i Azure Cosmos DB fungerar genom att lyssna på en Azure Cosmos DB-samling efter ändringar. Sedan returnerar den sorterade listan över dokument som har ändrats i den ordning som de har ändrats. Ändringarna sparas kan bearbetas asynkront och stegvis och utdata kan distribueras på en eller flera konsumenter för parallell bearbetning. 

Du kan läsa ändringsflödet på tre olika sätt, vilket beskrivs senare i den här artikeln:

*   [Använd Azure Functions](#azure-functions)
*   [Med hjälp av Azure Cosmos DB SDK](#sql-sdk)
*   [Med hjälp av Azure Cosmos DB-ändringen feed processor-biblioteket](#change-feed-processor)

Ändringsflöde är tillgänglig för varje partitionnyckelintervall inom dokumentsamlingen och därför kan distribueras på en eller flera konsumenter för parallell bearbetning, enligt följande bild.

![Distribuerad bearbetning av Azure Cosmos DB-ändringsflödet](./media/change-feed/changefeedvisual.png)

Ytterligare information:
* Ändringsfeed aktiveras som standard för alla konton.
* Du kan använda din [etablerat dataflöde](request-units.md) i skrivregion eller någon [läsa region](distribute-data-globally.md) för att läsa från den ändringsflödet, precis som alla andra åtgärder i Azure Cosmos DB.
* Ändringsflöde innehåller INSERT och update-åtgärder som utförs till dokument i samlingen. Du kan avbilda borttagningar genom att ange en ”mjuk borttagning”-flagga i dina dokument i stället för borttagningar. Du kan också ange en begränsad utgångstiden för dina dokument via den [TTL funktionen](time-to-live.md), till exempel 24 timmar och Använd värdet för egenskapen att samla in borttagningar. Med den här lösningen har att bearbeta ändringar inom ett kortare tidsintervall än TTL giltighetsperiod.
* Varje ändring till ett dokument visas exakt en gång i den ändringsflödet och klienter hantera deras kontrollpunkter logik. Biblioteket för change feed processor tillhandahåller automatiska kontrollpunkter och ”minst en gång” semantik.
* Den senaste ändringen för ett visst dokument ingår i ändringsloggen. Mellanliggande ändringar är kanske inte tillgänglig.
* Ändringsflöde sorteras begäran av ändring av inom varje partitionsnyckelvärde. Det finns ingen garanterad ordning över partitionsnyckel värden.
* Ändringar kan synkroniseras från alla point-in-time, det är ingen fast Datalagringsperiod ändringar är tillgängliga.
* Ändringar är tillgängliga i segment om viktiga partitionsintervall. Den här funktionen kan ändringar från stora samlingar som ska bearbetas parallellt med flera konsumenter/servrar.
* Program kan begära flera ändringen flöden samtidigt på samma samling.
* ChangeFeedOptions.StartTime kan användas för att ge en första startpunkt, till exempel, för att hitta fortsättningstoken motsvarande en given clock-tid. ContinuationToken, vinner om anges över värdena StartTime och StartFromBeginning. Precisionen för ChangeFeedOptions.StartTime är ~ 5 sekunder. 

## <a name="use-cases-and-scenarios"></a>Användningsområden och scenarier

Ändringsflöde möjliggör effektiv bearbetning av stora datauppsättningar med ett stort antal skrivningar och erbjuder ett alternativ till att fråga en hel datauppsättning för att identifiera vad som har ändrats. 

Till exempel med en ändringsflödet utföra du följande uppgifter effektivt:

* Uppdatera en cache, search-index eller ett informationslager med data som lagras i Azure Cosmos DB.
* Implementera programnivå data lagringsnivåer och arkivering, det vill säga datalagring ”frekvent” i Azure Cosmos DB och föråldrade ”kalla data” för att [Azure Blob Storage](../storage/common/storage-introduction.md) eller [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Utföra noll driftstopp migrering till ett annat Azure Cosmos DB-konto med ett annat partitioneringsschema.
* Implementera [lambda pipelines på Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) med Azure Cosmos DB. Azure Cosmos DB tillhandahåller en lösning för skalbar databas som kan hantera både skrivbelastning och frågeprestanda och implementera lambda-arkitektur med låg total Ägandekostnad. 
* Tar emot och lagring av händelsedata från enheter, sensorer, infrastruktur och program och bearbetar dessa händelser i realtid med [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md), eller [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

Följande bild visar hur lambda-pipelines som både mata in och fråga med hjälp av Azure Cosmos DB kan använda för att ändra feed support: 

![Azure Cosmos DB-baserade lambda-pipeline för skrivbelastning och frågeprestanda](./media/change-feed/lambda.png)

Dessutom inom din [serverlös](http://azure.com/serverless) webbprogram och mobilappar, kan du spåra händelser som ändringar av din kunds profil, inställningar eller plats att utlösa vissa åtgärder som att skicka push-meddelanden till sina enheter med hjälp av [Azure Functions](#azure-functions). Om du använder Azure Cosmos DB för att skapa ett spel, kan du, till exempel använda ändringsflödet att implementera i realtid rankningslistor baserat på poäng från färdiga spel.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Använd Azure Functions 

Om du använder Azure Functions, är det enklaste sättet att ansluta till en Azure Cosmos DB-ändringsflödet att lägga till en Azure Cosmos DB-utlösare i din Azure Functions-app. När du skapar en Azure Cosmos DB-utlösare i en Azure Functions-app kan du välja Azure Cosmos DB-samling för att ansluta till och funktionen som utlöses när en ändring i samlingen görs. 

Utlösare kan skapas i Azure Functions-portalen i Azure Cosmos DB-portalen eller via programmering. Mer information finns i [Azure Cosmos DB: databas utan Server databehandling med Azure Functions](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Med SDK

Den [SQL SDK](sql-api-sdk-dotnet.md) för Azure Cosmos DB ger dig all kraft att läsa och hantera en ändringsflödet. Men med stora krafter kommer många av ansvarsområden, för. Om du vill hantera kontrollpunkter, hantera dokument sekvensnummer och få detaljerad kontroll över partitionsnycklar kan med SDK: N vara den rätta inställningen.

Det här avsnittet beskriver hur du använder SQL-SDK för att arbeta med en ändringsflödet.

1. Börja med att läsa följande resurser från appconfig. Instruktioner om hur du hämtar nyckeln slutpunkt och auktoriseringsnyckel finns i [uppdatera din anslutningssträng](create-sql-api-dotnet.md#update-your-connection-string).

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

3. Hämta partitionen nyckelintervall:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Anropa ExecuteNextAsync för varje partitionsnyckelintervall:

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
> I stället för `ChangeFeedOptions.PartitionKeyRangeId`, du kan använda `ChangeFeedOptions.PartitionKey` att ange en enda partitionsnyckel som du vill hämta en ändringsflödet. Till exempel `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Om du har flera läsare kan du använda **ChangeFeedOptions** att distribuera skrivskyddade belastning till olika trådar eller olika klienter.

Och det var allt – med de här några rader kod kan du börja läsa ändringsflöde. Du kan hämta den fullständiga koden som används i den här artikeln från den [GitHub-lagringsplatsen](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

I koden i steg 4 ovan, den **ResponseContinuation** under senaste raden innehåller det sista logiska sekvensnumret (LSN) i dokumentet som du ska använda nästa gång du läser nya dokument efter den här sekvensnummer. Med hjälp av den **StartTime** av den **ChangeFeedOption** så kan du utvidga din net för att hämta dokument. Om din **ResponseContinuation** är null, men din **StartTime** går tillbaka i tiden och sedan får du alla dokument som har ändrats sedan den **StartTime**. Men om din **ResponseContinuation** har ett värde så att systemet får du alla dokument sedan den LSN.

Checkpoint-matris hindrar därför bara LSN för varje partition. Men om du inte vill hantera partitionerna kontrollpunkter, LSN, starttid, etc. enklare alternativ är att använda ändringsflödet processor-biblioteket.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Med hjälp av ändringen feed processor-biblioteket 

Den [processor-biblioteket för Azure Cosmos DB-ändringsfeed](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) kan hjälpa dig att enkelt distribuera händelsebearbetning bland olika konsumenter. Det här biblioteket förenklar läsning ändringar i partitioner och flera trådar som arbetar parallellt.

Den största fördelen med biblioteket change feed processor är att du inte behöver hantera varje partition och fortsättningstoken och du behöver inte avsöka varje samling manuellt.

Biblioteket för change feed processor förenklar läsning ändringar i partitioner och flera trådar som arbetar parallellt.  Den hanterar automatiskt läsning ändringar mellan partitioner som använder en mekanism för lånet. Som du ser i följande bild, om du startar två klienter som använder ändringsflödet processor-biblioteket kan de dela upp arbete sinsemellan. När du fortsätter att öka klienterna behålla de dividera arbete sinsemellan.

![Distribuerad bearbetning av Azure Cosmos DB-ändringsflödet](./media/change-feed/change-feed-output.png)

Vänstra klienten startades första och det igång med att övervaka alla partitionerna och andra klienten har startats och sedan först släpper några av lån till andra klienter. Som du ser det här är bra sätt att fördela arbetet mellan olika datorer och klienter.

Observera att om du har två serverlösa Azure funktioner som krävs för övervakning i samma samling och använder samma lånet och sedan de två funktionerna kan få olika dokument, beroende på hur processor-biblioteket beslutar att processen partitionerna.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Förstå ändringen feed processor-biblioteket

Det finns fyra huvudsakliga komponenter för att implementera biblioteket för change feed processor: övervakade samlingen, lånsamling, värd för händelsebearbetning och konsumenter. 

> [!WARNING]
> Det finns priseffekter med att skapa en samling eftersom du reserverar genomströmning för programmet för att kommunicera med Azure Cosmos DB. Mer information finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Insamling av övervakade:** övervakade samlingen är data som genereras ändringsflöde. Alla infogningar och ändringar i övervakade samlingen återspeglas i ändringsflödet i samlingen. 

**Lånsamling:** lånet samling koordinaterna bearbetning ändringsflöden över flera arbetare. En separat samling används som omfattar lånen med ett lån per partition. Nu är det bra att lagra den här lånsamling på ett annat konto med skrivningsregionen närmare till där ändringsflödet processor körs. Ett lån-objekt innehåller följande attribut: 
* Ägare: Anger den värd som äger lånet
* Fortsättning: Anger placeringen (fortsättningstoken) i ändringsflödet för en viss partition
* Tidsstämpel: Senast lånet har uppdaterats; tidsstämpeln som kan användas för att kontrollera om lånet tas i beaktande har upphört att gälla 

**Värd för händelsebearbetning:** varje värd avgör hur många partitioner för att bearbeta baserat på hur många andra instanser av värdar har aktiva lån. 
1.  När en värd startas, skaffar lån för att balansera arbetsbelastningen över alla värdar. En värd förnyas regelbundet lån, så lån är aktiva. 
2.  En värd kontrollpunkter senaste fortsättningstoken att lånet för varje läsa. För att säkerställa säkerheten för samtidighet, kontrollerar en värd ETag för varje uppdatering för lånet. Andra strategier för kontrollpunkt stöds också.  
3.  Vid avstängning, en värd släpper alla lån men behålls fortsättning-information så att den kan återuppta läsning från den lagrade kontrollpunkten senare. 

Antalet värdar får inte vara större än antalet partitioner (lån) just nu.

**Konsumenter:** konsumenter eller arbeten är trådar som utför bearbetningen ändringsflödet initieras av varje värd. Varje värd för händelsebearbetning kan ha flera konsumenter. Varje konsument läser ändringen flöde från den partition som den är tilldelad till och meddelar dess värden för ändringar och upphört att gälla lån.

För att bättre förstå hur dessa fyra element i ändringsfeed processor fungerar tillsammans, nu ska vi titta på ett exempel i följande diagram. Övervakade samlingen lagrar dokument och använder ”stad” som partitionsnyckel. Vi kan se att den blå partitionen innehåller dokument med fältet ”Stad” från ”A-E” och så vidare. Det finns två värdar med två konsumenter att läsa från de fyra partitionerna parallellt. Pilarna visar konsumenterna läsning från en specifik plats i den ändringsflödet. I den första partitionen representerar mörkare blå olästa ändringar medan ljusblått representerar redan Läs ändringar på den ändringsflödet. Värdarna använda lease-samlingen för att lagra ett värde för ”fortsättning” för att hålla reda på den aktuella läsning positionen för varje konsument. 

![Med hjälp av Azure Cosmos DB-ändringen feed värden för händelsebearbetning](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Arbeta med ändringsflödet processor-biblioteket

Innan du installerar ändringen feed processor NuGet-paketet, installera först: 

* Microsoft Azure documentdb, senaste versionen.
* Newtonsoft.Json, senaste versionen

Installera sedan den [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) och inkludera den som referens.

Att implementera i biblioteket för change feed processor som du behöver göra följande:

1. Implementera en **DocumentFeedObserver** objekt som implementerar **IChangeFeedObserver**.
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

2. Implementera en **DocumentFeedObserverFactory**, vilka implementerar en **IChangeFeedObserverFactory**.
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

5. skapa den **ChangeFeedProcessorBuilder** när du har definierat med motsvarande objekt 

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

Det är allt. Efter de här stegen för några dokument startar visas i den **DocumentFeedObserver.ProcessChangesAsync** metod.

Ovanför kod finns i bild syfte att visa olika slags objekt och deras interaktion. Du måste ange rätt variabler och starta dem med rätt värden. Du kan hämta den fullständiga koden som används i den här artikeln från den [GitHub-lagringsplatsen](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> Du bör aldrig ha en huvudnyckel i koden eller i konfigurationsfilen som ovan i kod. Se [hur du använder Key Vault att läsa nycklarna](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Vilka är de olika sätt som du kan läsa Change Feed? och när du använder varje metod?

Det finns tre alternativ att läsa ändringsfeed:

* **[Med hjälp av Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   Med den här metoden kan få du låg nivå av kontroll på ändringsfeed. Du kan hantera kontrollpunkten kan du komma åt en viss partition viktiga osv. Om du har flera läsare kan du använda [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) att distribuera skrivskyddade belastning till olika trådar eller olika klienter. .

* **[Med hjälp av Azure Cosmos DB-ändringen feed processor-biblioteket](#change-feed-processor)**

   Om du vill flytta över mycket av komplexiteten med ändringsflödet kan du använda biblioteket change feed processor. Det här biblioteket döljer komplexiteten mycket, men fortfarande ger dig fullständig kontroll på ändringsflödet. Det här biblioteket följer en [Övervakare mönstret](https://en.wikipedia.org/wiki/Observer_pattern), bearbetning av funktionen anropas av SDK: N. 

   Om du har en hög genomströmning ändringsflödet kan du kan skapa en instans av flera klienter för att läsa ändringsflöde. Eftersom du använder ”change feed processor-biblioteket”, kommer den automatiskt dela belastningen mellan olika klienter. Du behöver inte göra något. Alla komplexitet hanteras av SDK. Men om du vill ha en egen belastningsutjämnare kan du implementera IParitionLoadBalancingStrategy för Anpassad partitionering strategi. Implementera IPartitionProcessor – för anpassad bearbetning ändringar på en partition. Med SDK, men du kan bearbeta flera partitioner men om du vill bearbeta en viss partitionsnyckel du att behöva använda SDK för SQL-API.

* **[Använd Azure Functions](#azure-functions)** 
   
   Det sista alternativet Azure-funktion är det enklaste alternativet. Vi rekommenderar att du använder det här alternativet. När du skapar en Azure Cosmos DB-utlösare i en Azure Functions-app kan du välja Azure Cosmos DB-samling för att ansluta till och funktionen som utlöses när en ändring i samlingen görs. Titta på en [skärmen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) med att använda Azure fungerar och ändringsflödet

   Utlösare kan skapas i Azure Functions-portalen i Azure Cosmos DB-portalen eller via programmering. Visual Studio och VS Code har bra stöd för att skriva Azure-funktion. Du kan skriva och felsöka koden på skrivbordet och sedan distribuera en funktion med ett enda klick. Mer information finns i [Azure Cosmos DB: databas utan Server databehandling med Azure Functions](serverless-computing-database.md) artikeln.

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Vad är sorteringsordningen för dokument i ändringsflödet?

Ändringsfeed dokument kommer efter deras ändringstid. Den här sorteringsordningen garanteras per partition.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>För ett konto för flera regioner, vad händer med ändringsflödet när den skrivregionen misslyckas över? Ändringen feed också redundans? Ändringsfeed fortfarande visas sammanhängande eller skulle round orsaken ändringsflödet att återställa?

Ja, ändringsfeed fungerar i åtgärden manuell växling vid fel och det ska vara sammanhängande.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Hur länge ändringsfeed spara ändrade data om jag anger egenskapen TTL (Time to Live) för dokumentet till-1?

Ändringsfeed behålls alltid. Om data inte tas bort, förblir den i ändringsfeed.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Hur kan jag konfigurera Azure functions för att läsa från en viss region, eftersom ändringsfeed är tillgängliga i alla skrivskyddade regioner som standard?

Det är för närvarande inte möjligt att konfigurera Azure Functions för att läsa från en viss region. Det finns ett GitHub-ärende i Azure Functions-lagringsplatsen för att ange de önskade regionerna för alla Azure Cosmos DB-bindning och utlösare.

Azure Functions använder standardprincipen för anslutningen. Du kan konfigurera anslutningsläge i Azure Functions och som standard, den läsningar från skrivregionen så det är bäst att samplacera Azure Functions i samma region.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Vad är standardstorleken för batchar i Azure Functions?

100 dokument vid varje anrop av Azure Functions. Det här talet kan dock konfigureras i function.json-filen. Här är klar [lista med konfigurationsalternativ](../azure-functions/functions-run-local.md). Om du utvecklar lokalt, uppdatera programinställningar inom den [local.settings.json](../azure-functions/functions-run-local.md) fil.

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Jag övervakar en samling och läsa dess ändringen feed, men jag ser jag inte får infogad dokumentet, några dokument saknas. Vad som händer här?

Kontrollera att det inte finns någon funktion som läser i samma samling med samma lånsamling. Det hände till mig och senare jag insåg saknas dokumenten bearbetas av min andra Azure functions, som också använder samma lånet.

Därför, om du skapar flera Azure Functions för att läsa samma ändringsflödet och de måste använda olika lånsamling eller använda ”leasePrefix”-konfigurationen för att dela samma samling. När du använder biblioteket change feed processor du kan starta flera instanser av din funktion och SDK: N delar dokument mellan olika instanser automatiskt åt dig.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Dokumentet uppdateras varje sekund och jag får inte alla ändringar i Azure Functions lyssnar om du vill ändra feed.

Azure Functions enkäter ändringsfeed i varje 5 sekunder så försvinner alla ändringar mellan 5 sekunder. Azure Cosmos DB lagrar bara en version för var femte sekund, så du får 5 ändringen på dokumentet. Om du vill gå under 5 sekund och vill du hämta ändringsflödet varje sekund kan du konfigurera avsökningstiden ”feedPollTime”, se [Azure Cosmos DB-bindningar](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). Den har definierats i millisekunder med standardvärdet 5000. Är möjliga men inte lämpligt när du startar bränna mer Processorkraft nedan 1 sekund.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>Jag har infogat ett dokument i samlingen Mongo-API, men när jag hämta dokumentet i ändringsfeed visas ett annat id-värde. Vad är problemet här?

Samlingen är Mongo-API-samling. Kom ihåg att ändringsfeed läses med hjälp av SQL-klienten och Serialiserar objekt till JSON-format. På grund av formatering, JSON MongoDB klienter får ett matchningsfel mellan BSON formaterade dokument och JSON-formaterad ändringsflödet. Du ser är en representation av ett BSON-dokument i JSON. Om du använder binära attribut i en Mongo-konton måste konverteras de till JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Det är ett sätt att styra ändringsflödet för endast uppdateringar och infogar inte?

Inte idag, men den här funktionen är våra planer. Idag kan du lägga till en mjuk markör på dokumentet för uppdateringar.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Finns det ett sätt att hämta borttagningar i ändringsfeed?

För närvarande logga inte ändringsfeed borttagningar. Ändringsfeed förbättrar kontinuerligt och den här funktionen finns i översikten. Idag kan du lägga till en mjuk markör på dokumentet för borttagning. Lägg till ett attribut med dokumentet kallas ”borttagen” och ge den värdet ”true” och ange ett TTL-värde för dokumentet så att den kan tas bort automatiskt.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Kan jag läsa ändringsflöden för historiska dokument (till exempel dokument som har lagts till 5 år sedan)?

Ja, om dokumentet inte tas bort kan du läsa ändringen feed ur ursprung för din samling.

### <a name="can-i-read-change-feed-using-javascript"></a>Kan jag läsa ändringsflödet med hjälp av JavaScript?

Ja, Node.js SDK inledande stöd för ändringsfeed nyligen lagts till. Den kan användas som visas i följande exempel uppdatera documentdb-modulen till aktuella versionen innan du kör koden:

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

### <a name="can-i-read-change-feed-using-java"></a>Kan jag läsa ändringsflödet med Java?

Java-bibliotek för att läsa ändringsfeed är tillgänglig i [Github-lagringsplatsen](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). För närvarande finns Java-bibliotek dock några versioner bakom .NET-biblioteket. Båda biblioteken kommer snart att vara synkroniserade.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Kan jag använda _etag, _lsn eller _ts för interna bokföring som jag får som svar?

_etag format är intern och du bör inte lita på den (inte att parsa den) eftersom den kan ändras när som helst.
_ts är ändring eller skapa en tidsstämpel. Du kan använda _ts kronologisk jämförelse.
_lsn är är ett batch-id som läggs endast för ändringsfeed, representerar transaktions-id från store... Många dokument kan ha samma _lsn.
En sak att notera ETag på FeedResponse skiljer sig från _etag som du ser på dokumentet. _etag är en intern identifierare och används för samtidighet, meddelar om versionen av dokumentet och ETag används för ordningsföljd feeden.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Lägger du till extra kostnad genom att läsa ändringsfeed?

Du debiteras för RU: er används dvs. data flyttas till och från Azure Cosmos DB-samlingar använder alltid RU. Användare kommer att debiteras för RU som förbrukas av samlingen lånet.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Flera Azure Functions kan läsa en samling ändringsfeed?

Ja. Flera Azure Functions kan läsa ändringsfeed i samma samling. Azure Functions måste dock ha en separat leaseCollectionPrefix som definierats.

### <a name="should-the-lease-collection-be-partitioned"></a>Bör lånsamling partitioneras?

Nej, lånsamling kan åtgärdas. Partitionerade lånsamling behövs inte och den för närvarande inte stöds.

### <a name="can-i-read-change-feed-from-spark"></a>Kan jag läsa ändringen feed från Spark?

Ja, det kan du. Se [Azure Cosmos DB Spark Connector](spark-connector.md). Här är en [skärmen cast](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) som visar hur du kan bearbeta ändringsflödet som en strukturerad dataström.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Om jag bearbetar ändringsflödet med Azure Functions, säg en batch med 10 dokument och det uppstår ett fel vid 7 dokumentet. I så fall bearbetas de tre sista dokument inte hur startar jag bearbetning från det misslyckade dokumentet (dvs.) 7 dokument) i mitt nästa flödet?

Om du vill hantera felet är rekommenderat mönster du omsluter din kod med trycatch-block. Fånga felet och placera det dokumentet i en kö (förlorade) och sedan definiera logik för att hantera dokument som fel. Med den här metoden om du har en 200-dokumentet batch och ett enda dokument som misslyckades och behöver du inte slänga hela batchen.

Om felet inte ska du bakåt kontrollpunkt tillbaka till början kommer annars du kan ständigt dessa dokument från ändringsfeed. Kom ihåg att ändra feed är fortfarande den senaste sista snapin som visar dokument, på grund av det du kan förlora tidigare ögonblicksbild på dokumentet. ändringsfeed ser till att endast en senaste versionen av dokumentet och mellan andra processer kan komma och ändra dokumentet.

Som du hålla åtgärda koden, hittar du snart inga dokument på obeställbara meddelanden.
Azure Functions kallas automatiskt genom att ändra feed system och kontrollera återställningspunkt etc underhålls internt av Azure-funktion. Om du vill återställa kontrollpunkt och styra alla aspekter av det, bör du med hjälp av ändringen feed Processor SDK.


## <a name="next-steps"></a>Nästa steg

Läs mer om hur du använder Azure Cosmos DB med Azure Functions [Azure Cosmos DB: databas utan Server databehandling med Azure Functions](serverless-computing-database.md).

Använd följande resurser för mer information om hur du använder ändringsflödet processor-biblioteket:

* [På sidan med information](sql-api-sdk-dotnet-changefeed.md) 
* [Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Exempelkod som visar steg 1 – 6 ovan](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Ytterligare exempel på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Mer information om hur du använder ändringsflödet via SDK använder du följande resurser:

* [Informationssidan för SDK](sql-api-sdk-dotnet.md)
