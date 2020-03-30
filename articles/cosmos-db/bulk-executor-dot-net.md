---
title: Använda massutnämningsverkställare .NET-bibliotek i Azure Cosmos DB för massimport- och uppdateringsåtgärder
description: Massimport och uppdatering av Azure Cosmos DB-dokument med massutnämnaren .NET-biblioteket.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d7600267dcd196a9a5c06c29774ea21d582cd7ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246882"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Använd massutnämningen .NET-bibliotek för att utföra massåtgärder i Azure Cosmos DB

Den här självstudien innehåller instruktioner om hur du använder massutdelningsutdeltagaren .NET-biblioteket för att importera och uppdatera dokument till en Azure Cosmos-behållare. Mer information om massutflödesbiblioteket och hur det hjälper dig att utnyttja massivt dataflöde och lagringsutrymme finns i översiktsartikeln [för massutflödesbiblioteket.](bulk-executor-overview.md) I den här självstudien visas ett exempel på .NET-program som massimporterar slumpmässigt genererade dokument till en Azure Cosmos-behållare. När du har importerat den visas hur du kan masskopiera uppdatering av importerade data genom att ange korrigeringsfiler som åtgärder som ska utföras på specifika dokumentfält.

För närvarande stöds massutnrevarbiblioteket endast av Azure Cosmos DB SQL API- och Gremlin API-konton. I den här artikeln beskrivs hur du använder massutdrivaren .NET-biblioteket med SQL API-konton. Mer information om hur du använder massutnämnaren .NET-biblioteket med Gremlin API-konton finns [i utföra massåtgärder i Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Krav

* Om du inte redan har Installerat Visual Studio 2019 kan du hämta och använda [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Se till att du aktiverar "Azure-utveckling" under Visual Studio-installationen.

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.

* Du kan [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, kostnadsfritt och åtaganden. Du kan också använda [Azure Cosmos DB-emulatorn](https://docs.microsoft.com/azure/cosmos-db/local-emulator) med `https://localhost:8081` slutpunkten. Primärnyckeln finns i [Autentisera begäranden](local-emulator.md#authenticating-requests).

* Skapa ett Azure Cosmos DB SQL API-konto med hjälp av stegen som beskrivs i avsnittet [Skapa databaskonto](create-sql-api-dotnet.md#create-account) i snabbstartsartikeln .NET.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi byta till att arbeta med kod genom att ladda ner ett exempel .NET-program från GitHub. Det här programmet utför massåtgärder på data som lagras i ditt Azure Cosmos-konto. Om du vill klona programmet öppnar du en kommandotolk, navigerar till katalogen där du vill kopiera det och kör följande kommando:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Den klonade databasen innehåller två exempel "BulkImportSample" och "BulkUpdateSample". Du kan öppna något av exempelprogrammen, uppdatera anslutningssträngarna i Filen App.config med ditt Azure Cosmos DB-kontos anslutningssträngar, skapa lösningen och köra den.

Programmet "BulkImportSample" genererar slumpmässiga dokument och massimport importerar dem till ditt Azure Cosmos-konto. Programet "BulkUpdateSample" uppdaterar de importerade dokumenten genom att ange korrigeringar som åtgärder som ska utföras på specifika dokumentfält. I nästa avsnitt granskar du koden i var och en av dessa exempelappar.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Massimportdata till ett Azure Cosmos-konto

1. Navigera till mappen "BulkImportSample" och öppna filen "BulkImportSample.sln".  

2. Azure Cosmos DB:s anslutningssträngar hämtas från filen App.config enligt följande kod:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Massimportören skapar en ny databas och en behållare med databasnamn, behållarnamn och dataflödesvärden som anges i filen App.config.

3. Därefter initieras DocumentClient-objektet med direkt TCP-anslutningsläge:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor-objektet initieras med ett högt återförsöksvärde för väntetid och begränsade begäranden. Och sedan är de inställda på 0 för att passera överbelastning kontroll till BulkExecutor för sin livstid.  

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

5. Programmet anropar MassImportAsync API. .NET-biblioteket innehåller två överbelastningar av massimport-API: et - ett som accepterar en lista över serialiserade JSON-dokument och den andra som accepterar en lista över deserialiserade POCO-dokument. Mer information om definitionerna för var och en av dessa överbelastade metoder finns i [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **MassImportAsync-metoden accepterar följande parametrar:**
   
   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |enableUpsert    |   En flagga som aktiverar upsert-åtgärder i dokumenten. Om det redan finns ett dokument med det angivna ID:t uppdateras det. Som standard är den inställd på false.      |
   |inaktiveraAutomaticIdGeneration    |    En flagga för att inaktivera automatisk generering av ID. Som standard är den inställd på true.     |
   |maxConcurrencyPerPartitionKeyRange    | Den maximala graden av samtidighet per partition nyckelintervall, inställning till null kommer att orsaka biblioteket att använda ett standardvärde på 20. |
   |maxInMemorySorterandeBatchSize     |  Det maximala antalet dokument som hämtas från dokumentuppräknaren, som skickas till API-anropet i varje steg. För sorteringsfas i minnet som inträffar före massimport kommer det att leda till att biblioteket använder standardminivärde (documents.count, 10000000) om du ställer in den här parametern till null (documents.count, 10000000).       |
   |annulleringToken    |    Annulleringstoken för att smidigt avsluta massimportåtgärden.     |

   **Definition av svarsobjekt för massimport** Resultatet av api-anropet för massimport innehåller följande attribut:

   |**Parametern**  |**Beskrivning**  |
   |---------|---------|
   |AntaldocumentsImporterade (långa)   |  Det totala antalet dokument som har importerats från det totala antalet dokument som lämnats till api-anropet för massimport.       |
   |TotaltRequestUnitsConsumed (dubbel)   |   De totala begärandeenheter (RU) som förbrukas av API-anropet för massimport.      |
   |TotalTimeTaken (tidsspann)    |   Den totala tid det tar för massimport-API-anropet för att slutföra körningen.      |
   |BadInputDocuments (List-objekt\<>)   |     Listan över dokument i felformat som inte har importerats i api-anropet för massimport. Åtgärda de dokument som returneras och försök importera igen. Dokument med felaktiga format omfattar dokument vars ID-värde inte är en sträng (null eller någon annan datatyp anses ogiltig).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Massuppdateringsdata i ditt Azure Cosmos-konto

Du kan uppdatera befintliga dokument med hjälp av MassUpdateAsync API. I det här exemplet `Name` anger du fältet till `Description` ett nytt värde och tar bort fältet från de befintliga dokumenten. Den fullständiga uppsättningen uppdateringsåtgärder som stöds finns i [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

1. Navigera till mappen "BulkUpdateSample" och öppna filen "BulkUpdateSample.sln".  

2. Definiera uppdateringsobjekten tillsammans med motsvarande fältuppdateringsåtgärder. I det här exemplet `SetUpdateOperation` ska `Name` du `UnsetUpdateOperation` använda för `Description` att uppdatera fältet och ta bort fältet från alla dokument. Du kan också utföra andra operationer som att öka ett dokumentfält med ett visst värde, skicka specifika värden till ett matrisfält eller ta bort ett visst värde från ett matrisfält. Mer information om olika metoder som tillhandahålls av api:et för massuppdatering finns i [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Programmet anropar MassUpdateAsync API. Mer information om definitionen av metoden MassUpdateAsync finns i [API-dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **MassUpdateAsync-metoden accepterar följande parametrar:**

   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Den maximala graden av samtidighet per partitionsnyckelintervall, om du ställer in den här parametern på null, kommer biblioteket att använda standardvärdet(20).   |
   |maxInMemorySorterandeBatchSize    |    Det maximala antalet uppdateringsobjekt som hämtats från uppräknaren för uppdateringsobjekt som skickades till API-anropet i varje steg. För den sorteringsfas i minnet som inträffar före massuppdatering kommer det att leda till att biblioteket använder standardvärdet för minimivärdet(updateItems.count, 1000000).     |
   | annulleringToken|Annulleringstoken för att smidigt avsluta massuppdateringsåtgärden. |

   **Definition av svarsobjekt för massuppdatering** Resultatet av api-anropet för massuppdatering innehåller följande attribut:

   |**Parametern**  |**Beskrivning** |
   |---------|---------|
   |AntaldokumentUppdaterad (lång)    |   Antalet dokument som har uppdaterats av det totala antalet dokument som lämnats till API-anropet för massuppdatering.      |
   |TotaltRequestUnitsConsumed (dubbel)   |    De totala begärandeenheter (RU: er) som förbrukas av massuppdatering API-anropet.    |
   |TotalTimeTaken (tidsspann)   | Den totala tid det tar för massuppdaterings-API-anropet för att slutföra körningen. |
    
## <a name="performance-tips"></a>Prestandatips 

Tänk på följande punkter för bättre prestanda när du använder massutförandeörsbiblioteket:

* För bästa prestanda kan du köra ditt program från en virtuell Azure-dator som finns i samma region som ditt Azure Cosmos-kontos skrivregion.  

* Vi rekommenderar att du instansierar ett enda `BulkExecutor` objekt för hela programmet på en enda virtuell dator som motsvarar en specifik Azure Cosmos-behållare.  

* Eftersom en enda massåtgärd API-körning förbrukar en stor del av klientdatorns CPU och nätverks-IO (Detta händer genom att skapa flera aktiviteter internt). Undvik att skapa flera samtidiga uppgifter i programprocessen som kör API-anrop för massåtgärder. Om ett API-anrop för en enda massåtgärd som körs på en enda virtuell dator inte kan använda hela behållarens dataflöde (om behållarens dataflöde > 1 miljon RU/s) är det att föredra att skapa separata virtuella datorer för att samtidigt köra api-anrop för massåtgärd.  

* Kontrollera `InitializeAsync()` att metoden anropas efter instansiering av ett BulkExecutor-objekt för att hämta målkosmosbehållarens partitionskarta.  

* Se till att **gcServer** är aktiverat för bättre prestanda i programmets App.Config
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteket avger spår som kan samlas in antingen i en loggfil eller på konsolen. Om du vill aktivera båda lägger du till följande kod i programmets App.Config-fil.

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

* Mer information om Nuget-paketinformationen och viktig information finns i [massutredaren SDK-information](sql-api-sdk-bulk-executor-dot-net.md).
