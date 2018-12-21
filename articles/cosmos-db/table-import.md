---
title: Migrera befintliga data till ett tabell-API-konto i Azure Cosmos DB
description: Lär dig att migrera eller importera lokala eller molnbaserade data till ett Azure Tabell-API-konto i Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: b2256f16d284cb079231e271a7fc06c25c381c8a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53137692"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migrera data till Azure Cosmos DB Table-API-konto

Den här självstudiekursen beskriver hur du importerar data för användning med Azure Cosmos DB [Table-API:et](table-introduction.md). Om du har lagrade data i Azure Table Storage kan du använda datamigreringsverktyget eller AzCopy för att importera dina data till Azure Cosmos DB Table-API:t. Om du har data som lagras i ett Azure Cosmos DB Table API-konto (förhandsversion) måste du använda datamigreringsverktyget för att migrera data. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Importera data med datamigreringsverktyget
> * Importera data med AzCopy
> * Migrera från Table API (förhandsversion) till Table API 

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Öka dataflödet:** Hur lång tid datamigreringen tar beror på hur stort dataflöde du anger för en enskild container eller en uppsättning containrar. Du bör öka dataflödet för större datamigreringar. När du har slutfört migreringen kan du minska dataflödet för att sänka kostnaderna. Mer information om hur du ökar dataflödet i Azure Portal finns i avsnittet om prestandanivåer och prisnivåer i Azure Cosmos DB.

* **Skapa Azure Cosmos DB-resurser:** Innan du börjar migrera data skapar du alla dina tabeller i förväg från Azure-portalen. Om du migrerar till ett Azure Cosmos DB-konto som har dataflöde på databasnivå anger du en partitionsnyckel när du skapar Azure Cosmos DB-tabellerna.

## <a name="data-migration-tool"></a>Datamigreringsverktyget

Du kan använda det kommandoradsbaserade datamigreringsverktyget för Azure Cosmos DB (dt.exe) för att importera befintliga Azure Table Storage-data till ett Table API GA-konto, eller för att migrera data från ett Table API-konto (förhandsversion) till ett Table API GA-konto. Andra källor stöds inte för närvarande. Det går för närvarande inte att använda det användargränssnittsbaserade datamigreringsverktyget (dtui.exe) med Table API-konton. 

Utför följande åtgärder om du vill migrera tabelldata:

1. Ladda ned migreringsverktyget från [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Kör `dt.exe` med kommandoradsargumenten för ditt scenario. `dt.exe` stöder kommandon i följande format:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
```

Följande alternativ kan användas med kommandot:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Kommandoradsinställningar för källan

Använd följande alternativ för källan när du definierar Azure Table Storage eller Table API (förhandsversion) som källan för migreringen.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

När du importerar från Azure Table Storage hämtar du anslutningssträngen för källan genom att öppna Azure Portal och klicka på **Lagringskonton** > **Konto** > **Åtkomstnycklar** och kopierar sedan **anslutningssträngen** genom att klicka på kopieringsknappen.

![Skärmbild av alternativ för källan i HBase](./media/table-import/storage-table-access-key.png)

När du importerar från ett Azure Cosmos DB Table API-konto (förhandsversion) hämtar du anslutningssträngen för källan genom att öppna Azure Portal, klicka på **Azure Cosmos DB** > **Konto** > **Anslutningssträng** och kopierar sedan **anslutningssträngen** genom att klicka på kopieringsknappen.

![Skärmbild av alternativ för källan i HBase](./media/table-import/cosmos-connection-string.png)

[Azure Table Storage-exempelkommando](#azure-table-storage)

[Azure Cosmos DB Table API-exempelkommando (förhandsversion)](#table-api-preview)

### <a name="command-line-target-settings"></a>Kommandoradsinställningar för målet

Använd följande alternativ för målet när du definierar Azure Cosmos DB Table-API:et som målet för migreringen.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Exempel-kommando: Källa är Azure Table Storage

Här är ett kommandoradsexempel som visar hur du importerar från Azure Table Storage till Table API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Exempel-kommando: Källan är Azure Cosmos DB tabell-API (förhandsversion)

Här är ett kommandoradsexempel som visar hur du importerar från Table API (förhandsversion) till Table API GA:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrera data med AzCopy

Det andra alternativet som du kan använda när du migrerar data från Azure Table Storage till Azure Cosmos DB Table API är kommandoradsverktyget AzCopy. Om du vill använda AzCopy börjar du med att exportera dina data genom att följa anvisningarna i [Exportera data från Table Storage](../storage/common/storage-use-azcopy.md#export-data-from-table-storage) och importerar dem sedan till Azure Cosmos DB genom att följa anvisningarna i [Azure Cosmos DB Table API](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Använd följande exempel som referens när du importerar till Azure Cosmos DB. Observera att /Dest-värdet använder cosmosdb, inte core.

Exempel på importkommando:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrera från Table API (förhandsversion) till Table API

> [!WARNING]
> Om du genast vill dra nytta av fördelarna med allmänt tillgängliga tabeller i GA-versionen migrerar du dina befintliga tabeller i förhandsversionen genom att följa anvisningarna i det här avsnittet. Annars kommer vi att genomföra en automatisk migrering av befintliga kunder med förhandsversionen under de kommande veckorna. Observera att tabeller i förhandsversionen har vissa begränsningar som nyligen skapade tabeller inte har.
> 

Nu är Table API allmänt tillgängligt (GA). Det finns skillnader mellan förhandsversionen och GA-versionen för tabeller både i koden som körs i molnet och i koden som körs på klienten. Därför avråder vi dig från att försöka kombinera en SDK-klient som använder förhandsversionen med ett Table API-konto i GA-versionen och tvärtom. Kunder med förhandsversionen av Table API som vill fortsätta att använda sina befintliga tabeller men i en produktionsmiljö, måste migrera från förhandsversionen till GA-miljön eller vänta på den automatiska migreringen. Om du väljer att skjuta upp migreringen till den automatiska migreringen kommer du att informeras om begränsningarna i migrerade tabeller. Efter migreringen kommer du att kunna skapa nya tabeller i ditt befintliga konto utan begränsningar (endast migrerade tabeller har begränsningar).

Så här migrerar du från förhandsversionen av Table API till GA-versionen:

1. Skapa ett nytt Azure Cosmos DB-konto och ange kontots API-typ till Azure Table genom att följa anvisningarna i [Skapa ett databaskonto](create-table-dotnet.md#create-a-database-account).

2. Ändra klienterna så att de använder en GA-version (allmänt tillgänglig) av [Table API-SDK:erna](table-sdk-dotnet.md).

3. Migrera klientdata från tabeller i förhandsversionen till GA-tabeller med hjälp av datamigreringsverktyget. Anvisningar för hur du använder datamigreringsverktyget i detta syfte beskrivs i avsnittet om [datamigreringsverktyget](#data-migration-tool). 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera data med datamigreringsverktyget
> * Importera data med AzCopy
> * Migrera från Table API (förhandsversion) till Table API

Nu kan du fortsätta med nästa självstudiekurs där du lär dig hur du kör frågor mot data med hjälp av Azure Cosmos DB Table-API:et. 

> [!div class="nextstepaction"]
>[Hur kör jag frågor för att hämta data?](../cosmos-db/tutorial-query-table.md)
