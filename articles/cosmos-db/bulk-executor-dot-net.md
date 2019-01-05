---
title: Med hjälp av .NET-biblioteket för bulk-executor att utföra massimport och uppdateringsåtgärder i Azure Cosmos DB
description: Massimport och uppdatera Azure Cosmos DB-dokument med hjälp av bulk executor .NET-biblioteket.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d6fed65dad0e8f12495177b274cef181e9e91879
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044322"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Använd bulk executor .NET-biblioteket för att utföra massåtgärder i Azure Cosmos DB

Den här självstudiekursen innehåller anvisningar om hur du använder Azure Cosmos DB: s bulk executor .NET-biblioteket för att importera och uppdatera dokument till Azure Cosmos DB-behållare. Läs om bulk executor biblioteket och hur den hjälper dig att utnyttja massivt dataflöde och lagring i [bulk executor biblioteksöversikt](bulk-executor-overview.md) artikeln. I den här självstudiekursen visas ett .NET-program som bulk import slumpmässigt skapats dokument till en Azure Cosmos DB-behållare. När du har importerat visar den dig hur du kan masstilldela uppdatera importerade data genom att ange korrigeringar som åtgärder att utföra på specifika dokumentfält. 

Massinläsning executor biblioteket är för närvarande stöds av Azure Cosmos DB SQL API och Gremlin-API-konton. Den här artikeln beskriver hur du använder bulk executor-biblioteket för .NET med SQL API-konton. Läs om hur du använder .NET-biblioteket för bulk-executor med Gremlin-API i [utföra massåtgärder i Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Förutsättningar

* Om du inte redan har Visual Studio 2017 installerat, kan du hämta och använda den [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Se till att du aktiverar Azure-utveckling under installationen av Visual Studio.

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar. 

* Du kan [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. Du kan också använda den [Azure Cosmos DB-emulatorn](https://docs.microsoft.com/azure/cosmos-db/local-emulator) med den `https://localhost:8081` URI. Primärnyckeln finns i [Autentisera begäranden](local-emulator.md#authenticating-requests).

* Skapa ett Azure Cosmos DB SQL API-konto med hjälp av stegen som beskrivs i [skapa databaskonto](create-sql-api-dotnet.md#create-a-database-account) i .NET snabbstartsartikeln. 

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod genom att hämta vissa exempel .NET-program från GitHub. Dessa program utföra massåtgärder på Azure Cosmos DB-data. Öppna en kommandotolk för att klona programmen, gå till den katalog där du vill kopiera den och kör följande kommando:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Den klonade lagringsplatsen innehåller två exempel ”BulkImportSample” och ”BulkUpdateSample”. Du kan öppna antingen exempelprogrammen, uppdatera anslutningssträngarna i App.config-fil med anslutningssträngar för ditt Azure Cosmos DB-konto, skapa lösningen och köra den. 

”BulkImportSample” programmet genererar slumpmässiga dokument och bulk importerar dem till Azure Cosmos DB. Program-grupp ”BulkUpdateSample” uppdaterar importerade dokument genom att ange korrigeringar som åtgärder som ska utföras på specifika dokumentfält. I nästa avsnitt, ska du granska koden i var och en av dessa exempelappar.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Importera stora mängder data till Azure Cosmos DB

1. Navigera till mappen ”BulkImportSample” och öppna filen ”BulkImportSample.sln”.  

2. Azure Cosmos DB-anslutningssträngar hämtas från filen App.config som visas i följande kod:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Massinläsning importören skapar en ny databas och en samling med databasens namn, samlingsnamn och dataflöde värdena som anges i filen App.config. 

3. Bredvid initieras DocumentClient-objektet med anslutningsläge för direkt TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor-objektet har initierats med en hög retry-värdena för väntetid och begränsade begäranden. Och sedan de är inställda på 0 att skicka överbelastningskontroll till BulkExecutor för dess livslängd.  

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

5. Programmet anropar BulkImportAsync API: et. .NET-biblioteket innehåller två överlagringar av stora importera API – en som accepterar en lista med serialiserade JSON-dokument och den andra godkänner en lista över avserialiserade POCO-dokument. Mer information om definitionerna av var och en av dessa överlagrade metoder, som avser [API-dokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync metoden godkänner följande parametrar:**
   
   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |enableUpsert    |   En flagga för att aktivera upsert dokument. Om ett dokument med angiven id finns redan, den har uppdaterats. Som standard är den inställd på false.      |
   |disableAutomaticIdGeneration    |    En flagga för att inaktivera automatisk generering av ID. Som standard är den inställd till true.     |
   |maxConcurrencyPerPartitionKeyRange    | Högsta grad av samtidighet per partitionsnyckelintervall inställningen till null medför biblioteket för att använda ett standardvärde på 20. |
   |maxInMemorySortingBatchSize     |  Det maximala antalet dokument som hämtats från uppräknaren dokumentet som skickades till API-anrop i varje steg.  För InMemory-förväg bearbetnings sortering fasen före massimport medför inställningen till null bibliotek som ska användas av standardvärdet för min (documents.count 1000000).       |
   |cancellationToken    |    Token för annullering att avsluta massimport.     |

   **Bulk import svar objektdefinition** resultatet av massimport API-anropet innehåller följande attribut:

   |**Parametern**  |**Beskrivning**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Det totala antalet dokument som importerades från de dokument som angetts för stora importera API-anrop.       |
   |TotalRequestUnitsConsumed (double)   |   De totala begäransenheter (RU) som används av stora importera API-anrop.      |
   |TotalTimeTaken (TimeSpan)    |   Den totala tid som massimporten API-anrop för att slutföra körning.      |
   |BadInputDocuments (lista<object>)   |     Listan över dokument felaktig-format som inte har importerats i grupp importera API-anrop. Användaren bör åtgärda dokumenten som returneras och försök att importera igen. Felaktig-formaterade dokument innehålla dokument vars ID-värdet inte är en sträng (det är ogiltiga null eller andra datatype).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Uppdatera stora mängder data i Azure Cosmos DB

Du kan uppdatera befintliga dokument med hjälp av BulkUpdateAsync-API. I det här exemplet kommer du ange fältet namn till ett nytt värde och ta bort beskrivningsfältet från befintliga dokument. Uppdateringsåtgärder för den fullständiga uppsättningen fält som stöds, se [API-dokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Navigera till mappen ”BulkUpdateSample” och öppna filen ”BulkUpdateSample.sln”.  

2. Definiera update objekten tillsammans med motsvarande fält uppdateringsåtgärder. I det här exemplet ska du använda SetUpdateOperation för att uppdatera i namnfältet och UnsetUpdateOperation att ta bort fältet Beskrivning från alla dokument. Du kan också utföra andra åtgärder som att öka ett dokumentfält med ett specifikt värde, skicka specifika värden till ett matrisfält eller ta bort ett specifikt värde från ett matrisfält. Mer information om olika metoder som tillhandahålls av Massuppdatering API, som avser den [API-dokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Programmet anropar BulkUpdateAsync API: et. Läs om definitionen av metoden BulkUpdateAsync [API-dokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync metoden godkänner följande parametrar:**

   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Högsta grad av samtidighet per partitionsnyckelintervall inställningen till null medför bibliotek som ska användas standardvärdet 20.   |
   |maxInMemorySortingBatchSize    |    Det maximala antalet objekt att uppdatera hämtas från objekt-uppräknare uppdatering skickades till API-anrop i varje steg för InMemory-förväg bearbetnings sortering fas innan du uppdaterar bulk, inställningen till null medför bibliotek som ska användas av standardvärdet för min (updateItems.count, 1000000).     |
   | cancellationToken|Token för annullering att avsluta Massuppdatering. |

   **Massregistrera update svar objektdefinition** resultatet av API-anrop för massuppdatering innehåller följande attribut:

   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Det totala antalet dokument som har uppdaterats av de som angetts för massuppdatering API-anrop.      |
   |TotalRequestUnitsConsumed (double)   |    Totalt antal begäransenheter (RU) används av Massuppdatering API-anrop.    |
   |TotalTimeTaken (TimeSpan)   | Den totala tid som stora uppdatera API-anrop för att slutföra körning. |
    
## <a name="performance-tips"></a>Prestandatips 

Tänk på följande för bättre prestanda när du använder bulk executor bibliotek:

* Kör ditt program från en Azure virtuell dator som är i samma region som skrivregion Cosmos DB-konto för bästa prestanda.  

* Vi rekommenderar att skapa en instans av ett enda BulkExecutor-objekt för hela programmet i en enda virtuell dator som motsvarar en viss Cosmos DB-behållare.  

* Eftersom ett enda API massåtgärder förbrukar en stor del av klientdatorns processor- och IO. Detta sker genom att skapa flera aktiviteter internt bör du undvika att skapa flera samtidiga aktiviteter i din programprocessen varje körs samtidigt åtgärden API-anrop. Om ett enda bulk åtgärden API-anrop som körs på en virtuell dator inte kan använda hela behållaren dataflöde (om din behållare dataflöde > 1 miljon RU/s), är det bättre att skapa separata virtuella datorer för att samtidigt köra grupp åtgärden API-anrop.  

* Se till att InitializeAsync() anropas efter att ett BulkExecutor-objekt för att hämta partitionsöversikten för target Cosmos DB-behållare.  

* I programmets App.Config, se till att **gcServer** har aktiverats för bättre prestanda
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteket genererar spårningar som kan samlas på en loggfil eller i konsolen. Om du vill aktivera båda, lägger du till följande programmets App.Config.

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
* Mer information om Nuget-Paketinformation och viktig information för bulk executor .net-biblioteket, se[bulk information om SDK-executor](sql-api-sdk-bulk-executor-dot-net.md). 
