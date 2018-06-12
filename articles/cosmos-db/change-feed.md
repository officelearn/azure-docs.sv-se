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
ms.openlocfilehash: 2600565493a334c7227e5c0d67a5808f30751108
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261080"
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

* Microsoft.Azure.DocumentDB, version 1.13.1 eller senare 
* Newtonsoft.Json, version 9.0.1 eller senare

Installera sedan den [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) och inkludera den som en referens.

Att genomföra ändringen feed processor biblioteket som du behöver göra följande:

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

Det stämmer. När dessa några steg dokument startar till den **DocumentFeedObserver ProcessChangesAsync** metod. Hitta koden ovan i [GitHub-repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Vilka är de olika sätt som du kan läsa ändra Feed? och när du ska använda varje metod?

Det finns tre alternativ för att läsa ändra feed:

* **[Med hjälp av Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   Med den här metoden kan hämta du låg nivå på kontrollen i ändra feed. Du kan hantera kontrollpunkten, du kan komma åt en viss partition key osv. Om du har flera läsare, kan du använda [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) att distribuera skrivskyddade inläsning till olika trådar eller olika klienter. .

* **[Med hjälp av Azure Cosmos DB ändringen feed processor-bibliotek](#change-feed-processor)**

   Om du vill flytta ut mycket komplex ändra feed kan du använda ändra feed processor biblioteket. Det här biblioteket döljer komplexiteten mycket, men fortfarande ger du fullständig kontroll på Ändra feed. Det här biblioteket följer en [person mönster](https://en.wikipedia.org/wiki/Observer_pattern), bearbetning-funktionen anropas av SDK. 

   Om du har en hög genomströmning ändring feed kan du initiera flera klienter för att läsa ändra feeden. Eftersom du använder ”ändra feed processor biblioteket”, delar det automatiskt upp belastningen på olika klienter. Du behöver inte göra något. Alla komplexitet hanteras av SDK. Men om du vill ha en egen belastningsutjämnare kan du implementera IParitionLoadBalancingStrategy för Anpassad partitionering strategi. Implementera IPartitionProcessor – för anpassad bearbetning ändringar på en partition. Med SDK, men du kan bearbeta en partitionsintervall men om du vill bearbeta en viss partitionsnyckel du ha du använder SDK för SQL-API.

* **[Med hjälp av Azure Functions](#azure-functions)** 
   
   Det sista alternativet Azure-funktion är det enklaste alternativet. Vi rekommenderar att du använder det här alternativet. När du skapar en Azure DB som Cosmos-utlösare i en app i Azure Functions, väljer du Azure DB som Cosmos-samlingen för att ansluta till och funktionen utlöses när en ändring i samlingen. Titta på en [skärmen omvandlingen](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) med att använda Azure fungerar och ändra feed

   Utlösare kan skapas i Azure Functions-portalen i Azure DB som Cosmos-portalen eller programmässigt. Visual Studio och VS-koden har bra stöd för att skriva Azure-funktion. Du kan skriva felsöka koden på skrivbordet och sedan distribuera en funktion med ett enda klick. Mer information finns i [Azure Cosmos DB: serverlösa databasen datoranvändning med hjälp av Azure Functions](serverless-computing-database.md) artikel.

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Vad är sorteringsordningen för dokument i ändra feed?

Ändra feed dokument levereras i ordning efter deras ändringstid. Den här sorteringsordningen garanteras per partition.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>För ett konto för flera regioner, vad händer med ändringen feed när write-region misslyckas över? Ändringen feed också växling vid fel? Skulle feed fortfarande visas ändringen sammanhängande eller ändrar failover orsak feed att återställa?

Ja, ändra feed fungerar över manuella Redundansåtgärden och blir sammanhängande.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Hur länge kvarstå ändra feed ändrade data om jag egenskapen TTL (Time to Live) för dokumentet 1?

Ändra feed behålls alltid. Om data inte tas bort, förblir den i ändra feed.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Hur konfigurerar jag Azure functions för att läsa från en viss region som ändra feed finns tillgänglig i alla regioner som skrivskyddade som standard?

Det är för närvarande inte möjligt att konfigurera Azure Functions för att läsa från en viss region. Det finns en GitHub-problem i Azure Functions-lagringsplatsen för att ange prioriterade områden av alla Azure DB som Cosmos-bindning och utlösare.

Azure Functions använder standardprincipen för anslutningen. Du kan konfigurera anslutningsläge i Azure Functions och som standard, det läser från write-region så att det är bäst att samplacera Azure Functions i samma region.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Vad är standardstorleken för batchar i Azure Functions?

100 dokument vid varje anrop av Azure Functions. Det här antalet kan dock konfigureras i filen function.json. Här är klar [lista med konfigurationsalternativ](../azure-functions/functions-run-local.md). Om du utvecklar lokalt, uppdatera programinställningar inom den [local.settings.json](../azure-functions/functions-run-local.md) fil.

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Jag övervakar en samling och läsa dess ändra feed, men jag ser jag inte får infogade dokumentet, vissa dokument saknas. Vad som händer här?

Kontrollera att det inte finns någon funktion som läser samlingen med samma lease-samling. Det hände mig och senare jag insåg saknas dokumenten bearbetas av min andra Azure-funktioner, som också använder samma lånet.

Därför, om du skapar flera Azure Functions för att läsa samma ändra feed och de måste använda olika lån samlingen eller använda ”leasePrefix”-konfigurationen för att dela samma samling. När du använder ändra feed processor biblioteket kan du starta flera instanser av din funktion och SDK delar dokument mellan olika instanser automatiskt åt dig.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Dokumentet uppdateras varje sekund och jag får inte alla ändringar i Azure Functions lyssnar om du vill ändra feed.

Azure Functions avsökningar ändra feed för varje 5 sekunder så försvinner alla ändringar mellan 5 sekunder. Azure Cosmos-DB lagras en version 5 sekunder så du får 5 ändringen på dokumentet. Om du vill gå under 5 andra och vill avsöka ändra Feed varje sekund du konfigurera avsökning tid ”feedPollTime”, se [Azure Cosmos DB bindningar](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). Det har definierats i millisekunder med ett standardvärde på 5000. Är möjligt, men inte tillrådligt som börjar du bränna fler CPU nedan 1 sekund.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>Du har infogat en dokumentet samlingen Mongo-API, men när dokumentet visas i ändra feed, visas ett annat id-värde. Vad är problemet här?

Samlingen är Mongo API-samling. Kom ihåg att ändra feed läses med hjälp av SQL-klienten och Serialiserar objekt i JSON-format. På grund av formatering, JSON MongoDB klienter får ett matchningsfel mellan BSON formaterade dokument och JSON-formaterad ändra feed. Du ser representation av ett BSON dokument i JSON. Om du använder binära attribut i Mongo-konton kan konverteras de till JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Det är ett sätt att hantera förändringar feed endast för uppdateringar för och infogas inte?

Inte idag, men den här funktionen är aktiverad Översikt. Idag, kan du lägga till en icke-begränsande markör på dokumentet för uppdateringar.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Finns det ett sätt att få borttagningar i ändra feed?

För närvarande logga inte ändra feed borttagningar. Ändra feed förbättra kontinuerligt och den här funktionen har översikt. Idag, kan du lägga till en icke-begränsande markör på dokumentet för borttagning. Lägg till ett attribut på dokumentet kallas ”Borttaget” inställd på ”true” och ange ett TTL-värde för dokumentet så att den kan tas bort automatiskt.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Kan jag läsa ändra feed för historiska dokument (t.ex, dokument som har lagts till 5 år sedan)?

Ja, om dokumentet inte tas bort kan du läsa ändringen feeden så långt ursprung för din samling.

### <a name="can-i-read-change-feed-using-javascript"></a>Kan jag läsa ändra feed med hjälp av JavaScript?

Ja, Node.js SDK inledande stöd för ändring feed nyligen lagts till. Den kan användas som visas i följande exempel du uppdateringen documentdb modulen till aktuell version innan du kör kod:

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
