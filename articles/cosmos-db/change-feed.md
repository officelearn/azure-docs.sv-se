---
title: Arbeta med ändringen feeds stöd i Azure Cosmos DB | Microsoft Docs
description: Använda Azure Cosmos DB ändra feed stöd för att spåra ändringar i dokument och utföra händelsebaserat bearbetning som utlösare och uppdatera cacheminnen och analyser system kontinuerligt.
keywords: Ändra feed
services: cosmos-db
author: rafats
manager: jhubbard
editor: mimig
documentationcenter: ''
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 16f339540767246ef82bce020aacf9846c8d2e0b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
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

1.  [Med hjälp av Azure Functions](#azure-functions)
2.  [Med hjälp av Azure Cosmos DB SDK](#rest-apis)
3.  [Med hjälp av Azure Cosmos DB ändra Feed Processor-bibliotek](#change-feed-processor)

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

<a id="rest-apis"></a>
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

Om du har flera läsare, kan du använda **ChangeFeedOptions** att distribuera skrivskyddade inläsning till olika trådar eller olika klienter.

Och som är det, med dessa några rader kod kan du börja läsa ändra feeden. Du kan hämta den fullständiga koden som används i denna artikel från den [GitHub-repo-](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

I koden i steg 4 ovan, den **ResponseContinuation** under senaste raden innehåller den sista logiska sekvensnumret (LSN) av dokument som du ska använda nästa gång du läsa nya dokument efter den här sekvensnummer. Med hjälp av den **StartTime** av den **ChangeFeedOption** utvidga din net för att hämta dokumenten. I så fall om din **ResponseContinuation** är null, men din **StartTime** går tillbaka i tiden och sedan får du alla dokument som ändrats sedan den **StartTime**. Men om din **ResponseContinuation** har ett värde system får du alla dokument eftersom den LSN.

Så hindrar kontrollpunkt matrisen bara LSN för varje partition. Men om du inte vill hantera partitionerna kontrollpunkter, LSN, starttid, etc. enklare alternativet är att använda ändra Feed Processor biblioteket.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Använda ändra Feed Processor-bibliotek 

Den [Azure Cosmos DB ändra Feed Processor biblioteket](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) kan hjälpa dig att enkelt distribuera händelsebearbetning mellan flera användare. Det här biblioteket förenklar läsning ändringar i partitioner och flera trådar som arbetar parallellt.

Den största fördelen av ändra Feed Processor-biblioteket är att du inte behöver hantera varje partition och fortsättningstoken och du behöver inte söka varje samling manuellt.

Ändra Feed Processor bibliotek förenklar läsning ändringar i partitioner och flera trådar som arbetar parallellt.  Den hanterar automatiskt läsning ändringar över partitioner som använder en mekanism för lånet. Som du ser i följande bild, om du startar två klienter som använder ändra Feed Processor-biblioteket, delar de arbete sinsemellan. När du fortsätter att öka klienterna behålla de dividera arbete sinsemellan.

![Distribuerad bearbetning av Azure Cosmos DB ändra feed](./media/change-feed/change-feed-output.png)

Vänster klienten startades första och den startade övervakningen alla partitioner och andra klienten startades och sedan först släpper vissa lån till andra klienten. Du kan se det här är bra sätt att distribuera arbete mellan olika datorer och klienter.

Observera att om du har två serverlösa Azure funktioner som krävs för övervakning i samma samling och använder samma lånet och sedan de två funktionerna kan få olika dokument, beroende på hur processor-biblioteket beslutar att åtgärd partitionerna.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Förstå ändra Feed Processor-biblioteket

Det finns fyra huvudsakliga komponenter för att implementera ändra Feed Processor: samlingen övervakade, lån samlingen, värd för händelsebearbetning och konsumenterna. 

> [!WARNING]
> Skapa en samling har priser effekter, som du reserverar genomströmning för programmet för att kommunicera med Azure Cosmos DB. Mer information finns i [sida med priser](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Insamling av övervakade:** övervakade samlingen är data som ändras feeden genereras. Alla infogningar och ändringar i övervakade samlingen återspeglas i feeden ändring i mängden. 

**Insamling av lånet:** lån samling koordinaterna ändringen feed över flera arbetare bearbetas. En separat samling används för att lagra lån med en leasing per partition. Är det fördelaktigt för att lagra samlingen lån på ett annat konto med området skrivåtgärder närmare till där ändra Feed Processor körs. Ett lån-objekt innehåller följande attribut: 
* Ägare: Anger den värd som äger lånet
* Fortsättning: Anger placeringen (fortsättningstoken) i ändra flödet för en viss partition
* Tidsstämpel: Tid för senaste lån uppdaterades; tidsstämpeln kan användas för att kontrollera om lånet betraktas som upphört att gälla 

**Värd för händelsebearbetning:** varje värd anger hur många partitioner att bearbeta baserat på hur många andra instanser av värdar har aktiva lån. 
1.  När en värd startas, skaffar lån för att utjämna belastningen över alla värdar. En värd förnyas regelbundet lån, så att förbli lån. 
2.  En värd kontrollpunkter senaste fortsättningstoken till lånet för varje läsa. För att säkerställa säkerheten för samtidighet, kontrollerar en värd ETag för varje lease-uppdatering. Andra kontrollpunkt strategier stöds också.  
3.  En värd släpper alla lån vid avstängning men behålls fortsättning-information så att den kan återupptas senare läsning från den lagrade kontrollpunkten. 

Antalet värdar kan inte vara större än antalet partitioner (lån) just nu.

**Konsumenterna:** konsumenter eller arbetare, är trådar som behandlar ändra feed initieras av varje värd. Varje värd för händelsebearbetning kan ha flera konsumenter. Varje konsument läser ändringen feed från partitionen tilldelas och meddela dess värden för ändringar och gått lån.

För att bättre förstå hur dessa fyra element i ändra Feed Processor tillsammans, ska vi titta på ett exempel i följande diagram. Samlingen övervakade lagrar dokument och använder ”stad” som partitionsnyckel. Vi kan se att den blå partitionen innehåller dokument med fältet ”Ort” från ”A E” och så vidare. Det finns två värdar med två konsumenter läsning från de fyra partitionerna parallellt. Pilarna visar konsumenterna läsning från en specifik plats i ändringen feed. I den första partitionen representerar mörkare blå oläst ändringar medan lätta blå representerar redan skrivskyddade ändringarna på ändringen feed. Värdarna använda lån-samlingen för att lagra ett värde för ”fortsättning” för att hålla reda på den aktuella positionen för läsning för varje konsument. 

![Med hjälp av Azure Cosmos DB ändringen feed värd för händelsebearbetning](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Arbeta med ändra Feed Processor-biblioteket

Innan du installerar ändra Feed Processor NuGet-paketet måste först installera: 

* Microsoft.Azure.DocumentDB, version 1.13.1 eller senare 
* Newtonsoft.Json, version 9.0.1 eller senare

Installera sedan den [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) och inkludera den som en referens.

Att implementera ändra Feed Processor-biblioteket som du behöver göra följande:

1. Implementera en **DocumentFeedObserver** -objekt som implementerar **IChangeFeedObserver**.

2. Implementera en **DocumentFeedObserverFactory**, som implementerar en **IChangeFeedObserverFactory**.

3. I den **CreateObserver** metod för **DocumentFeedObserverFacory**, skapa en instans av den **ChangeFeedObserver** som du skapade i steg 1 och returnera den.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. Skapa en instans av **DocumentObserverFactory**.

5. Skapa en instans av en **ChangeFeedEventHost**:

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. Registrera den **DocumentFeedObserverFactory** med värden.

Koden för steg 4 till 6 är: 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

Det stämmer. När dessa några steg dokument startar till den **DocumentFeedObserver ProcessChangesAsync** metod.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure Cosmos DB med Azure Functions finns [Azure Cosmos DB: serverlösa databasen datoranvändning med hjälp av Azure Functions](serverless-computing-database.md).

Mer information om hur du använder biblioteket ändra Feed Processor använder du följande resurser:

* [Informationssidan](sql-api-sdk-dotnet-changefeed.md) 
* [Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Exempelkod som visar steg 1 – 6 ovan](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Ytterligare exempel på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Mer information om hur du använder ändringen feed via SDK använder du följande resurser:

* [Informationssidan om SDK](sql-api-sdk-dotnet.md)
