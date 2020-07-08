---
title: Migrera från bulk utförar-biblioteket till Mass stödet i Azure Cosmos DB .NET v3 SDK
description: Lär dig hur du migrerar ditt program från att använda utförar-biblioteket för Mass support i Azure Cosmos DB SDK v3
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.openlocfilehash: 1f204b6d73f121b8f05c807d6be47c36c006f607
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261434"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrera från bulk utförar-biblioteket till Mass stödet i Azure Cosmos DB .NET v3 SDK

I den här artikeln beskrivs de steg som krävs för att migrera ett befintligt programs kod som använder [.net bulk utförar-biblioteket](bulk-executor-dot-net.md) till funktionen för [Mass stöd](tutorial-sql-api-dotnet-bulk-import.md) i den senaste versionen av .NET SDK.

## <a name="enable-bulk-support"></a>Aktivera Mass stöd

Aktivera Mass support på `CosmosClient` instansen via [AllowBulkExecution](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) -konfigurationen:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Skapa aktiviteter för varje åtgärd

Mass support i .NET SDK fungerar genom att använda det [parallella aktivitets bibliotek](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) och de grupperings åtgärder som inträffar samtidigt. 

Det finns ingen enskild metod i SDK som tar din lista över dokument eller åtgärder som en indataparameter, men i stället måste du skapa en uppgift för varje åtgärd som du vill köra i bulk och sedan vänta tills de har slutförts.

Om din första Indatatyp till exempel är en lista med objekt där varje objekt har följande schema:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Om du vill göra Mass import (liknar att använda BulkExecutor. BulkImportAsync) måste du ha samtidiga anrop till `CreateItemAsync` . Ett exempel:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Om du vill göra en Mass *uppdatering* (liknar att använda [BulkExecutor. BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)) måste du ha samtidiga anrop till `ReplaceItemAsync` metoden efter att objektet har uppdaterats. Ett exempel:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

Och om du vill utföra Mass *borttagning* (liknar att använda [BulkExecutor. BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)) måste du ha samtidiga anrop till `DeleteItemAsync` med och- `id` partitionerings nyckeln för varje objekt. Ett exempel:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Resulterande tillstånd för inspelnings uppgift

I föregående kod exempel har vi skapat en samtidig lista med aktiviteter och anropade `CaptureOperationResponse` metoden för var och en av dessa aktiviteter. Den här metoden är ett tillägg som låter oss underhålla ett *liknande svars schema* som BulkExecutor, genom att samla in eventuella fel och spåra [användningen av enheter för programbegäran](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Där `OperationResponse` deklareras som:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Köra åtgärder samtidigt

Vi använder den här hjälp klassen för att spåra omfattningen av hela listan med aktiviteter:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

`ExecuteAsync`Metoden väntar tills alla åtgärder har slutförts och du kan använda den så här:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Samla in statistik

Föregående kod väntar tills alla åtgärder har slutförts och beräknar den statistik som krävs. Den här statistiken liknar den för utförar-bibliotekets [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse`Innehåller:

1. Den totala tid det tar att bearbeta listan över åtgärder via Mass stöd.
1. Antalet lyckade åtgärder.
1. Totalt antal förbrukade enheter för programbegäran.
1. Om det finns fel visas en lista med tupler som innehåller undantaget och det associerade objektet för loggning och identifiering.

## <a name="retry-configuration"></a>Försök att konfigurera igen

Bulk utförar Library hade en [vägledning](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) som nämndes att ange `MaxRetryWaitTimeInSeconds` och `MaxRetryAttemptsOnThrottledRequests` för [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) för `0` att delegera kontroll till biblioteket.

Det finns inget dolt beteende för Mass support i .NET SDK. Du kan konfigurera alternativen för återförsök direkt via [CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) och [CosmosClientOptions. MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> I de fall där de etablerade enheterna är mycket lägre än vad som förväntas utifrån mängden data, kanske du vill överväga att ange dessa till höga värden. Mass åtgärden tar längre tid, men den har en högre chans att lyckas på grund av högre återförsök.

## <a name="performance-improvements"></a>Prestandaförbättringar

Precis som med andra åtgärder med .NET SDK ger data Ströms-API: erna bättre prestanda och undviker onödig serialisering. 

Det går bara att använda Stream-API: er om de data som du använder matchar data strömmens data strömmar (till exempel fil strömmar). I sådana fall `CreateItemStreamAsync` `ReplaceItemStreamAsync` `DeleteItemStreamAsync` `ResponseMessage` `ItemResponse` ökar det data flöde som kan uppnås genom att använda metoderna,, eller och arbeta med (i stället för).

## <a name="next-steps"></a>Nästa steg

* Mer information om .NET SDK-versioner finns i artikeln [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) .
* Hämta den fullständiga [käll koden för migrering](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) från GitHub.
* [Ytterligare bulk-exempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
