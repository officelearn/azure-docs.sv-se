---
title: "Importera data för användning med Azure Cosmos DB tabell API | Microsoft Docs"
description: "Lär dig hur importerar data som ska användas med Azure Cosmos DB tabell API."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: mimig
ms.openlocfilehash: 5163d20aece01addddeae93cb07355bde928a440
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importera data för användning med Azure Cosmos DB tabell API

Den här självstudiekursen innehåller instruktioner om hur du importerar data för användning med Azure Cosmos DB [tabell API](table-introduction.md). Om du har data som lagras i Azure Table storage kan använda du antingen Datamigreringsverktyg eller AzCopy för att importera dina data. Om du har data som lagras i en Azure Cosmos DB tabell API (förhandsgranskning), måste du använda Migreringsverktyget för Data för att migrera data. När du har importerat data kommer du att kunna dra nytta av funktioner för premium Azure Cosmos DB erbjudanden, till exempel NYCKELFÄRDIGT global distributionsplatsen, dedikerad genomströmning, en siffra millisekunders latens vid den 99th percentilen garanteras hög tillgänglighet och automatisk sekundära indexering.

Den här kursen ingår följande uppgifter:

> [!div class="checklist"]
> * Importera data med verktyget datamigrering
> * Importera data med AzCopy
> * Migrera från tabellen API (förhandsgranskning) till tabellen API 

## <a name="data-migration-tool"></a>Verktyg för migrering

Azure Cosmos DB datamigrering kommandoradsverktyget (dt.exe) kan användas för att importera dina befintliga Azure Table storage data till en tabell API GA-konto eller migrera data från en tabell-API (förhandsgranskning)-kontot till ett tabell-API GA-konto. Andra källor stöds inte för närvarande. Användargränssnittet baserat datamigrering verktyget (dtui.exe) stöds inte för närvarande för tabellen API-konton. 

Om du vill utföra en migrering av tabelldata, utför följande uppgifter:

1. Hämta Migreringsverktyget från [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Kör `dt.exe` med kommandoradsargument för ditt scenario.

DT.exe tar ett kommando i följande format:

    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 

Alternativ för kommandot är:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Inställningar för kommandoraden datakälla

Använd följande källa alternativ när du definierar förhandsversionen av Azure-tabellagring eller tabell API som källa för migreringen.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Öppna Azure-portalen för att hämta anslutningssträngen för datakällan vid import från Azure Table storage, och klicka på **lagringskonton** > **konto**  >   **Åtkomstnycklar**, och Använd kopieringsknappen för att kopiera den **anslutningssträngen**.

![Skärmbild av HBase alternativ](./media/table-import/storage-table-access-key.png)

För att hämta anslutningssträngen för datakällan vid import från ett konto i Azure Cosmos DB tabell API (förhandsgranskning), öppnar du den Azure klickar du på **Azure Cosmos DB** > **konto**  >  **Anslutningssträngen** och Använd kopieringsknappen för att kopiera den **anslutningssträngen**.

![Skärmbild av HBase alternativ](./media/table-import/cosmos-connection-string.png)

[Azure Table Storage Exempelkommando](#azure-table-storage)

[Exempelkommando Azure Cosmos DB tabell API (förhandsgranskning)](#table-api-preview)

### <a name="command-line-target-settings"></a>Kommandoradsverktyget Målinställningar

Använd följande alternativ för mål när du definierar Azure Cosmos DB tabell API som mål för migreringen.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Exempelkommando: källan är Azure Table storage

Här är ett kommandoradsverktyg exempel som visar hur du importerar från Azure Table storage tabell-API: et:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey==<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Exempelkommando: källan är Azure Cosmos DB tabell API (förhandsgranskning)

Här följer ett exempel på kommandoraden att importera från tabellen API preview till tabellen API GA:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="azcopy-command"></a>AzCopy-kommandot

Med hjälp av kommandoradsverktyget azcopy är ett annat alternativ för att migrera data från Azure Table storage Azure Cosmos DB tabell-API: et. Om du vill använda AzCopy måste du först exportera dina data enligt beskrivningen i [exportera data från tabellagring](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), importera data till Azure Cosmos DB enligt beskrivningen i [Azure Cosmos DB tabell API](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

När du utför importen till Azure Cosmos DB, finns i följande exempel. Observera att värdet för/dest används cosmosdb inte core.

Importera kommando:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrera från tabellen API (förhandsgranskning) till tabellen API

> [!WARNING]
> Om du vill att omedelbart dra nytta av fördelarna allmänt tillgängliga tabeller och sedan migrera dina befintliga preview tabeller som anges i det här avsnittet, annars vi kommer att utföra automatisk migrering under de kommande veckorna befintliga preview kunder Observera men som automatiskt migrerade har preview tabeller vissa begränsningar för dem som nyligen inte skapats tabeller kommer.
> 

Tabell-API är nu allmänt tillgänglig (GA). Det finns skillnader mellan förhandsgranskning och GA versioner av tabeller både i den kod som körs i molnet och kod som körs på klienten. Därför är det inte bäst att försöka blanda en förhandsgranskning SDK-klient med en GA tabell API-konto, och vice versa. Tabellen API-kunder som vill fortsätta att använda sina befintliga tabeller, men i en produktionsmiljö måste du migrera från förhandsversionen i GA-miljön eller vänta tills automatisk migrering. Om du väntar vid automatisk migrering meddelas om begränsningarna för migrerade tabeller. Efter migreringen kommer du att kunna skapa nya tabeller i ditt befintliga konto utan begränsningar (endast migrerade tabeller har begränsningar).

För att migrera från tabellen API: et (förhandsgranskning) allmänt tillgänglig tabell-API: et.

1. Skapa ett nytt Azure DB som Cosmos-konto och ange dess API-typ som Azure Table enligt beskrivningen i [skapa ett databaskonto](create-table-dotnet.md#create-a-database-account).

2. Ändra klienter använder en GA-versionen av den [tabell API SDK](table-sdk-dotnet.md).

3. Migrera klientdata från preview tabeller till GA-tabeller med hjälp av verktyget för migrering av Data. Instruktioner om hur du använder datamigreringsverktyget för detta beskrivs i [datamigreringsverktyget](#data-migration-tool). 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera data med verktyget datamigrering
> * Importera data med AzCopy
> * Migrera från tabellen API (förhandsgranskning) till tabellen API

Du kan nu gå vidare till nästa kurs och lär dig hur du frågar efter data med hjälp av Azure Cosmos DB tabell-API. 

> [!div class="nextstepaction"]
>[Hur du frågar efter data?](../cosmos-db/tutorial-query-table.md)
