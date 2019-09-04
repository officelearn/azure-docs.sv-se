---
title: Använda Mass utförar .NET-bibliotek för att utföra Mass import och uppdaterings åtgärder i Azure Cosmos DB
description: Mass import och uppdatering Azure Cosmos DB dokument med hjälp av bulk utförar .NET Library.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 26e4c4edbcfc282fffd0d43673d3585cfe973848
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241269"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Använd Mass utförar .NET-bibliotek för att utföra Mass åtgärder i Azure Cosmos DB

Den här självstudien innehåller instruktioner om hur du använder Azure Cosmos DBs .NET-bibliotek för Mass utförar för att importera och uppdatera dokument till Azure Cosmos-behållaren. Om du vill lära dig mer om bulk utförar Library och hur det hjälper dig att använda massivt data flöde och lagring, se [översikts](bulk-executor-overview.md) artikel för utförar-bibliotek. I den här självstudien får du se ett exempel på en .NET-app som Mass importer genererar slumpmässigt genererade dokument till en Azure Cosmos-behållare. När du har importerat visar det hur du kan massredigera importerade data genom att ange korrigeringar som åtgärder att utföra på vissa dokument fält. 

Massinläsning executor biblioteket är för närvarande stöds av Azure Cosmos DB SQL API och Gremlin-API-konton. Den här artikeln beskriver hur du använder bulk utförar .NET-bibliotek med SQL API-konton. Läs om hur du använder .NET-biblioteket för bulk-executor med Gremlin-API i [utföra massåtgärder i Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Förutsättningar

* Om du inte redan har Visual Studio 2019 installerat kan du ladda ned och använda [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Se till att du aktiverar Azure-utveckling under installationen av Visual Studio.

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar. 

* Du kan [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. Du kan också använda [Azure Cosmos DB emulatorn](https://docs.microsoft.com/azure/cosmos-db/local-emulator) med `https://localhost:8081` slut punkten. Primärnyckeln finns i [Autentisera begäranden](local-emulator.md#authenticating-requests).

* Skapa ett Azure Cosmos DB SQL API-konto med hjälp av stegen som beskrivs i avsnittet [skapa databas konto](create-sql-api-dotnet.md#create-account) i artikeln om .net-snabb start. 

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi växla till att arbeta med kod genom att hämta några exempel på .NET-program från GitHub. De här programmen utför Mass åtgärder på Azure Cosmos DB data. Om du vill klona programmen öppnar du en kommando tolk och navigerar till den katalog där du vill kopiera dem och kör följande kommando:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Den klonade lagrings platsen innehåller två exempel: "BulkImportSample" och "BulkUpdateSample". Du kan öppna något av exempel programmen, uppdatera anslutnings strängarna i app. config-filen med anslutnings strängarna i Azure Cosmos DB-kontot, skapa lösningen och köra den. 

Programmet "BulkImportSample" genererar slumpmässiga dokument och Mass importer dem till Azure Cosmos DB. Program Mass uppdateringen "BulkUpdateSample" uppdaterar de importerade dokumenten genom att ange korrigeringar som åtgärder att utföra på specifika dokument fält. I nästa avsnitt kommer du att granska koden i vart och ett av dessa exempel appar.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Importera stora mängder data till Azure Cosmos DB

1. Gå till mappen "BulkImportSample" och öppna filen "BulkImportSample. SLN".  

2. Azure Cosmos DBens anslutnings strängar hämtas från filen app. config, vilket visas i följande kod:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Mass import programmet skapar en ny databas och en behållare med databas namnet, behållar namnet och data flödes värden som anges i filen app. config. 

3. Nästa DocumentClient-objekt initieras med direkt TCP-anslutnings läge:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor-objektet initieras med ett högt återförsöks värde för vänte tid och begränsade begär Anden. Och de ställs in på 0 för att skicka överbelastnings kontroll till BulkExecutor för dess livs längd.  

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

5. Programmet anropar BulkImportAsync-API: et. .NET-biblioteket innehåller två överlagringar av Mass import-API: en som accepterar en lista med serialiserade JSON-dokument och den andra accepterar en lista med deserialiserade POCO-dokument. Mer information om definitionerna för var och en av dessa överlagrade metoder finns i [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync-metoden accepterar följande parametrar:**
   
   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |enableUpsert    |   En flagga som aktiverar upsertar av dokumenten. Om ett dokument med angiven ID finns redan, den har uppdaterats. Som standard är den inställd på falskt.      |
   |disableAutomaticIdGeneration    |    En flagga för att inaktivera automatisk generering av ID. Som standard är den inställd till true.     |
   |maxConcurrencyPerPartitionKeyRange    | Den högsta graden av samtidighet per partitionsnyckel, med värdet null, gör att bibliotek använder ett standardvärde på 20. |
   |maxInMemorySortingBatchSize     |  Det maximala antalet dokument som hämtas från uppräkna ren som skickas till API-anropet i varje steg.  För bearbetnings fasen för för hands bearbetning i minnet innan Mass import, gör inställningen null att bibliotek använder standardvärdet min (Documents. Count, 1000000).       |
   |cancellationToken    |    Token för att avbryta Mass import på ett smidigt sätt.     |

   **Objekt definition för Mass import av svar** Resultatet av API-anropet för Mass import innehåller följande attribut:

   |**Parametern**  |**Beskrivning**  |
   |---------|---------|
   |NumberOfDocumentsImported (lång)   |  Det totala antalet dokument som importerades från de dokument som angetts för stora importera API-anrop.       |
   |TotalRequestUnitsConsumed (dubbel)   |   De totala begäransenheter (RU) som används av stora importera API-anrop.      |
   |TotalTimeTaken (TimeSpan)    |   Den totala tid som massimporten API-anrop för att slutföra körning.      |
   |BadInputDocuments (list\<objekt >)   |     Listan över dokument felaktig-format som inte har importerats i grupp importera API-anrop. Användaren bör åtgärda dokumenten som returneras och försök att importera igen. Felaktig-formaterade dokument innehålla dokument vars ID-värdet inte är en sträng (det är ogiltiga null eller andra datatype).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Uppdatera stora mängder data i Azure Cosmos DB

Du kan uppdatera befintliga dokument med hjälp av BulkUpdateAsync-API. I det här exemplet kommer du ange fältet namn till ett nytt värde och ta bort beskrivningsfältet från befintliga dokument. En fullständig uppsättning fält uppdaterings åtgärder som stöds finns i [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Gå till mappen "BulkUpdateSample" och öppna filen "BulkUpdateSample. SLN".  

2. Definiera uppdaterings objekt tillsammans med motsvarande fält uppdaterings åtgärder. I det här exemplet ska du använda SetUpdateOperation för att uppdatera i namnfältet och UnsetUpdateOperation att ta bort fältet Beskrivning från alla dokument. Du kan också utföra andra åtgärder som att öka ett dokumentfält med ett specifikt värde, skicka specifika värden till ett matrisfält eller ta bort ett specifikt värde från ett matrisfält. Mer information om olika metoder som tillhandahålls av Mass uppdaterings-API: et finns i [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Programmet anropar BulkUpdateAsync-API: et. Mer information om definitionen av BulkUpdateAsync-metoden finns i API- [dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync-metoden accepterar följande parametrar:**

   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Den högsta graden av samtidighet per partitionsnyckel, med värdet null, gör att bibliotek använder standardvärdet 20.   |
   |maxInMemorySortingBatchSize    |    Det maximala antalet uppdaterings objekt som hämtats från den uppräknare för uppdaterings objekt som har skickats till API-anropet i varje steg för sorterings fasen före bearbetning i minnet före Mass uppdatering, gör att bibliotek använder standardvärdet min (updateItems. Count, 1000000).     |
   | cancellationToken|Avbrotts-token för att avsluta Mass uppdatering på ett smidigt sätt. |

   **Definition av svars objekt för Mass uppdatering** Resultatet av API-anropet för Mass uppdatering innehåller följande attribut:

   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |NumberOfDocumentsUpdated (lång)    |   Det totala antalet dokument som har uppdaterats av de som angavs till API-anropet för Mass uppdatering.      |
   |TotalRequestUnitsConsumed (dubbel)   |    Totalt antal begäransenheter (RU) används av Massuppdatering API-anrop.    |
   |TotalTimeTaken (TimeSpan)   | Den totala tid som stora uppdatera API-anrop för att slutföra körning. |
    
## <a name="performance-tips"></a>Prestandatips 

Tänk på följande för bättre prestanda när du använder bulk executor bibliotek:

* För bästa prestanda bör du köra ditt program från en virtuell Azure-dator som finns i samma region som ditt Cosmos DB konto skriv region.  

* Vi rekommenderar att du instansierar ett enskilt BulkExecutor-objekt för hela programmet inom en enskild virtuell dator som motsvarar en specifik Cosmos-behållare.  

* Eftersom ett enda API massåtgärder förbrukar en stor del av klientdatorns processor- och IO. Detta sker genom att skapa flera aktiviteter internt bör du undvika att skapa flera samtidiga aktiviteter i din programprocessen varje körs samtidigt åtgärden API-anrop. Om ett API-anrop med en enda Mass åtgärd som körs på en enskild virtuell dator inte kan använda hela behållarens data flöde (om behållarens data flöde > 1 000 000 RU/s), är det bättre att skapa separata virtuella datorer för körning samtidigt API-anrop för Mass åtgärder.  

* Se till att InitializeAsync () anropas efter att ha instansierat ett BulkExecutor-objekt för att hämta mål Cosmos container partition Map.  

* Kontrol lera att **gcServer** har Aktiver ATS för bättre prestanda i programmets app. config
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteket genererar spår som kan samlas in antingen i en loggfil eller i-konsolen. Om du vill aktivera båda lägger du till följande i programmets app. config.

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
* Information om NuGet-paket och viktig information om utförar .NET-bibliotek finns i[UTFÖRAR SDK-information](sql-api-sdk-bulk-executor-dot-net.md). 
