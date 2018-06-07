---
title: Använda BulkExecutor .NET-bibliotek för att utföra massåtgärder i Azure Cosmos DB | Microsoft Docs
description: Använd Azure Cosmos DB BulkExecutor .NET-biblioteket massimport och uppdatera dokument till Azure Cosmos DB samlingar.
keywords: .NET bulk utförare
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 0e8c5f9a848eaa1543ce9d58895b035e23d9f335
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611168"
---
# <a name="using-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Använda BulkExecutor .NET-bibliotek för att utföra massåtgärder i Azure Cosmos DB

Den här självstudiekursen innehåller instruktioner om hur du använder Azure Cosmos DB BulkExecutor .NET-bibliotek att importera och uppdatera dokument till Azure Cosmos DB samlingar. Läs om BulkExecutor bibliotek och hur du kan utnyttja omfattande genomströmning och lagring i [BulkExecutor biblioteket – översikt](bulk-executor-overview.md) artikel. Den här kursen får du via ett .NET-program som Massredigera import slumpmässigt genererade dokument till en Azure DB som Cosmos-samling. När du har importerat visar den du hur du kan massimportera uppdatera data som importeras genom att ange korrigeringsprogram som åtgärder att utföra på specifika dokumentfält.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte redan har Visual Studio 2017 installerat, du kan hämta och använda den [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Kontrollera att du aktiverar Azure-utveckling under installationen av Visual Studio.

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar. 

* Du kan [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. Du kan använda den [Azure Cosmos DB emulatorn](https://docs.microsoft.com/azure/cosmos-db/local-emulator) med den `https://localhost:8081` URI. Den primärnyckeln finns i [för autentisering av förfrågningar](local-emulator.md#authenticating-requests).

* Skapa ett Azure SQL DB-API Cosmos-konto med hjälp av stegen som beskrivs i [skapa databaskonto](create-sql-api-dotnet.md#create-a-database-account) i .NET quickstart artikeln. 

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi växla till att arbeta med kod genom att hämta vissa exempel .NET-program från GitHub. Dessa program utför massåtgärder på Azure Cosmos DB data. Öppna en kommandotolk för att klona programmen, gå till den katalog där du vill kopiera dem och kör följande kommando:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Den klonade lagringsplatsen innehåller två samplingarna ”BulkImportSample” och ”BulkUpdateSample”. Du kan öppna antingen exempelprogrammen, uppdatera anslutningssträngarna i App.config-fil med ditt konto Azure Cosmos DB-anslutningssträngar, skapa lösningen och köra den. 

”BulkImportSample” programmet genererar slumpmässiga dokument och bulk importerar dem till Azure Cosmos DB. ”BulkUpdateSample” program samtidigt uppdaterar importerade dokument genom att ange korrigeringsprogram som åtgärder att utföra på specifika dokumentfält. I nästa avsnitt ska du granska koden i var och en av dessa exempelappar.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Massinläsning importera data till Azure Cosmos DB

1. Navigera till mappen ”BulkImportSample” och öppna filen ”BulkImportSample.sln”.  

2. Azure Cosmos DB-anslutningssträngar hämtas från filen App.config som visas i följande kod:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Importverktyget för bulk skapar en ny databas och en samling med databasens namn, samlingsnamn och genomströmning värdena som anges i filen App.config. 

3. Nästa har DocumentClient-objektet initierats med direkta TCP-uppkoppling-läge:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor-objektet har initierats med en hög försök värden för väntetiden och begränsas begäranden. Och sedan de har angetts till 0 att skicka överbelastningskontroll till BulkExecutor för dess livslängd.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Programmet anropar BulkImportAsync-API. .NET-bibliotek innehåller två överlagringar av flesta importera API – som accepterar en lista med serialiserade JSON-dokument och andra accepterar en lista över avserialiserat POCO-dokument. Mer information om definitionerna för var och en av dessa överlagrade metoder, referera till [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Metoden BulkImportAsync accepterar följande parametrar:**
   
   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |enableUpsert    |   En flagga för att aktivera upsert dokument. Om ett dokument med angiven id finns redan, uppdateras. Som standard anges till false.      |
   |disableAutomaticIdGeneration    |    En flagga för att inaktivera automatisk generering av ID. Som standard är den inställd på true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maximal graden av samtidighet per partitionsnyckelintervallet inställningen till null kommer biblioteket för att använda standardvärdet 20. |
   |maxInMemorySortingBatchSize     |  Det maximala antalet dokument hämtas från uppräknaren dokumentet som skickades till API-anrop i varje steg.  I minnet före bearbetning sortering steget innan massimport kommer inställningen till null biblioteket för att använda standardvärdet för min (documents.count 1000000).       |
   |cancellationToken    |    Annullering token avsluta massimport.     |

   **Massredigera Importera svar objektdefinitionen** massimport API-anrop som innehåller följande attribut:

   |**Parametern**  |**Beskrivning**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Det totala antalet dokument som importeras av dokument som angetts för flesta importera API-anrop.       |
   |TotalRequestUnitsConsumed (double)   |   De totala frågeenheter (RU) som används av flesta importera API-anrop.      |
   |TotalTimeTaken (TimeSpan)    |   Den totala tid som massimporten API-anrop för att köras.      |
   |BadInputDocuments (lista över<object>)   |     En lista med felaktigt format dokument som inte har importerats i flesta importera API-anrop. Användaren måste åtgärda de dokument som returneras och försök importera. Felaktig-formaterade dokument innehåller dokument vars ID-värdet inte är en sträng (det är ogiltiga null eller andra datatype).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Uppdatera stora mängder data i Azure Cosmos DB

Du kan uppdatera befintliga dokument med BulkUpdateAsync API. I det här exemplet ska du ange fältet namn till ett nytt värde och ta bort beskrivningsfältet från befintliga dokument. För en fullständig uppsättning stöds fältet uppdateringsåtgärder, referera till [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Navigera till mappen ”BulkUpdateSample” och öppna filen ”BulkUpdateSample.sln”.  

2. Definiera update objekten tillsammans med motsvarande fält uppdateringsåtgärder. I det här exemplet använder du SetUpdateOperation för att uppdatera fältet namn och UnsetUpdateOperation bort beskrivningsfältet från alla dokument. Du kan också utföra andra åtgärder som ökningen ett dokumentfält med ett specifikt värde, push specifika värden i en matris fält eller ta bort ett specifikt värde från en array-fält. Mer information om olika metoder som tillhandahålls av Massuppdatering API, referera till den [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Programmet anropar BulkUpdateAsync-API. Mer information om definitionen av metoden BulkUpdateAsync, referera till [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Metoden BulkUpdateAsync accepterar följande parametrar:**

   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Maximal graden av samtidighet per partitionsnyckelintervallet inställningen till null kommer biblioteket för att använda standardvärdet 20.   |
   |maxInMemorySortingBatchSize    |    Det maximala antalet objekt att uppdatera hämtas från uppräknaren update objekt skickades till API-anrop i varje steg i minnet före bearbetning sortering fasen före uppdatering av bulk inställningen till null kommer biblioteket för att använda standardvärdet för min (updateItems.count, 1000000).     |
   | cancellationToken|Annullering token avsluta Massuppdatering. |

   **Massredigera update svar objektdefinitionen** Massuppdatering API-anrop som innehåller följande attribut:

   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Det totala antalet dokument som har uppdaterats av de som angetts för massuppdatering API-anrop.      |
   |TotalRequestUnitsConsumed (double)   |    Totalt antal begäranden enheter (RU) används av Massuppdatering API-anrop.    |
   |TotalTimeTaken (TimeSpan)   | Den totala tid som flesta uppdatera API-anrop för att köras. |
    
## <a name="performance-tips"></a>Prestandatips 

Tänk på följande för bättre prestanda när du använder BulkExecutor bibliotek:

* Kör ditt program från en Azure-dator som är i samma region som din Cosmos DB kontot Skriv region för bästa prestanda.  

* Det rekommenderas att skapa en instans av ett objekt för hela programmet i en enskild virtuell dator som motsvarar en viss samling Cosmos DB BulkExecutor.  

* Eftersom en enda API massåtgärder förbrukar en stor del av den klientdatorn CPU: N och IO. Detta sker genom att skapa flera uppgifter internt bör du undvika att skapa flera samtidiga uppgifter i din programprocessen varje körs samtidigt åtgärden API-anrop. Om ett enda bulk åtgärden API-anrop som körs på en enskild virtuell dator kan inte använda din hela samlingen genomströmning (om din samling genomströmning > 1 miljon RU/s), är det bättre att skapa separata virtuella datorer för att samtidigt köra grupp åtgärden API-anrop.  

* Kontrollera InitializeAsync() anropas efter att ett BulkExecutor-objekt för att hämta partitionsöversikten för mål-DB Cosmos samling.  

* I ditt program App.Config kontrollerar **gcServer** har aktiverats för bättre prestanda
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteket avger spårningar som kan samlas in på en loggfil eller i konsolen. Om du vill aktivera både lägger du till följande programmets App.Config.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
```

## <a name="next-steps"></a>Nästa steg
* Mer information om Nuget-Paketinformation och viktig information för BulkExecutor .net-bibliotek finns[BulkExecutor SDK information](sql-api-sdk-bulk-executor-dot-net.md). 
