---
title: Migrera från massutnrönarbiblioteket till masssupporten i Azure Cosmos DB .NET V3 SDK
description: Lär dig hur du migrerar ditt program från att använda massutrålningsbiblioteket till masssupporten i Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: maquaran
ms.openlocfilehash: e1a2a5d849d3c94d62b8645c41f288ba130aa6a4
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479336"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrera från massutnrönarbiblioteket till masssupporten i Azure Cosmos DB .NET V3 SDK

I den här artikeln beskrivs de steg som krävs för att migrera ett befintligt programs kod som använder [.NET-massutdrivarbiblioteket](bulk-executor-dot-net.md) till [masssupportfunktionen](tutorial-sql-api-dotnet-bulk-import.md) i den senaste versionen av .NET SDK.

## <a name="enable-bulk-support"></a>Aktivera masssupport

Aktivera massstöd `CosmosClient` för instansen via [allowbulkexecution-konfigurationen:](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Skapa aktiviteter för varje operation

Massstöd i .NET SDK fungerar genom att utnyttja [det parallella aktivitetsbiblioteket](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) och grupperingsåtgärderna som sker samtidigt. 

Det finns ingen enskild metod som tar listan över dokument eller åtgärder som en indataparameter, utan du måste skapa en aktivitet för varje åtgärd som du vill köra i grupp.

Om din första indata till exempel är en lista över objekt där varje objekt har följande schema:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Om du vill göra massimport (liknande med BulkExecutor.BulkImportAsync) måste du `CreateItemAsync` ha samtidiga anrop till med varje artikelvärde. Ett exempel:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Om du vill göra *massuppdatering* (liknande med [BulkExecutor.BulkUpdateAsync)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)måste du `ReplaceItemAsync` ha samtidiga anrop till metoden när du har uppdaterat artikelvärdet. Ett exempel:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

Och om du vill göra *massborttagning* (liknande med [BulkExecutor.BulkDeleteAsync)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)måste du `DeleteItemAsync`ha `id` samtidiga anrop till , med och partitionsnyckeln för varje objekt. Ett exempel:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Fånga aktivitetsresultattillstånd

I de föregående kodexemplen har du skapat en samtidig lista `CaptureOperationResponse` över aktiviteter och anropat metoden för var och en av dessa aktiviteter. Denna metod är ett tillägg som låter oss upprätthålla ett *liknande svar schema* som BulkExecutor, genom att fånga eventuella fel och spåra begäran enheter [användning](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Om `OperationResponse` den deklareras som

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Utföra åtgärder samtidigt

När listan över uppgifter har definierats väntar du tills alla är klara. Du kan spåra slutförandet av aktiviteterna genom att definiera omfattningen av massåtgärden enligt följande kodavsnitt:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Samla in statistik

Den tidigare koden väntar tills alla åtgärder har slutförts och beräknar den statistik som krävs. Den här statistiken liknar den i bulk executor-bibliotekets [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

Innehåller: `BulkOperationResponse`

1. Den totala tid det tar att bearbeta listan över åtgärder via masssupport.
1. Antalet lyckade operationer.
1. Summan av begärandeenheter som förbrukats.
1. Om det finns fel visas en lista över tupplar som innehåller undantaget och det associerade objektet för loggning och identifiering.

## <a name="performance-improvements"></a>Prestandaförbättringar

Precis som med andra åtgärder med .NET SDK resulterar dataflödets API:er i bättre prestanda och undviker onödig serialisering. 

Det är bara möjligt att använda strömma API:er om vilken typ av data du använder matchar en ström av byte (till exempel filströmmar). I sådana fall `CreateItemStreamAsync`ökar `ReplaceItemStreamAsync`dataflödet som kan uppnås genom att använda , eller `DeleteItemStreamAsync` metoder och arbeta med `ResponseMessage` (i stället för ). `ItemResponse`

## <a name="next-steps"></a>Nästa steg

* Mer information om .NET SDK-versionerna finns i artikeln [Azure Cosmos DB SDK.](sql-api-sdk-dotnet.md)
* Hämta den fullständiga [migreringskällakoden](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) från GitHub.
* [Ytterligare massprover på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
