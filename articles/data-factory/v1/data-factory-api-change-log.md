---
title: Data Factory - .NET API-ändringslogg
description: Beskriver bryta ändringar, funktionstillägg, buggfixar och så vidare, i en specifik version av .NET API för Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: dbbbdebdcf1db7afe485166f5744f2291b757d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74979010"
---
# <a name="azure-data-factory---net-api-change-log"></a>Ändringslogg för Azure Data Factory – .NET API
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Den här artikeln innehåller information om ändringar i Azure Data Factory SDK i en viss version. Du hittar det senaste NuGet-paketet för Azure Data Factory [här](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Version 4.11.0
Funktionstillägg:

* Följande länkade tjänsttyper har lagts till:
  * [PåPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Följande datauppsättningstyper har lagts till:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Följande kopierar källtyper har lagts till:
  * [MongoDbKälla](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Version 4.10.0
* Följande valfria egenskaper har lagts till i TextFormat:
  * [SkipLineCount (SkipLineCount)](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FörstaRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Följande länkade tjänsttyper har lagts till:
  * [PåPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Följande datauppsättningstyper har lagts till:
  * [PåPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Följande kopierar källtyper har lagts till:
  * [CassandraKälla](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Lägg till [egenskapen WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) i AzureMLBatchExecutionActivity
  * Aktivera överföring av flera webbtjänstindata till ett Azure Machine Learning-experiment

## <a name="version-491"></a>Version 4.9.1
### <a name="bug-fix"></a>Bugg fix
* Inaktuell WebApi-baserad autentisering för [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Version 4.9.0
### <a name="feature-additions"></a>Funktionstillägg
* Lägg till egenskaper för [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) och [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) i CopyActivity. Se [Stegvis kopia](data-factory-copy-activity-performance.md#staged-copy) för mer information om funktionen.

### <a name="bug-fix"></a>Bugg fix
* Introducera en överbelastning av [metoden ActivityWindowOperationExtensions.List,](https://msdn.microsoft.com/library/mt767915.aspx) som tar en [ActivityWindowsByActivityListParameters-instans.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx)
* Markera [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) och [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) som valfritt i CopySink.

## <a name="version-480"></a>Version 4.8.0
### <a name="feature-additions"></a>Funktionstillägg
* Följande valfria egenskaper har lagts till i kopiera aktivitetstyp för att aktivera justering av kopieringsprestanda:
  * [ParallelCopies (ParallelCopies)](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Version 4.7.0
### <a name="feature-additions"></a>Funktionstillägg
* Lade till ny StorageFormat-typ [OrcFormat-typ](https://msdn.microsoft.com/library/mt723391.aspx) för att kopiera filer i ORC-format (Optimized Row columnar).
* Lägg till [egenskaperna AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) och PolyBaseSettings i SqlDWSink.
  * Gör det möjligt att använda PolyBase för att kopiera data till SQL Data Warehouse.

## <a name="version-461"></a>Version 4.6.1
### <a name="bug-fixes"></a>Felkorrigeringar
* Åtgärdar HTTP-begäran om att lista aktivitetsfönster.
  * Tar bort resursgruppsnamnet och datafabriksnamnet från begäran.

## <a name="version-460"></a>Version 4.6.0
### <a name="feature-additions"></a>Funktionstillägg
* Följande egenskaper har lagts till [i PipelineProperties:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Datamängder](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Följande egenskaper har lagts till [i PipelineRuntimeInfo:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)
  * [PipelineState (PipelineState)](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Lade till ny [StorageFormat-typ](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) [JsonFormat-typ](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) för att definiera datauppsättningar vars data är i JSON-format.

## <a name="version-450"></a>Version 4.5.0
### <a name="feature-additions"></a>Funktionstillägg
* Lade till [liståtgärder för aktivitetsfönster](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Lade till metoder för att hämta aktivitetsfönster med filter baserat på entitetstyperna (det vill än datafabriker, datauppsättningar, pipelines och aktiviteter).
* Följande länkade tjänsttyper har lagts till:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Följande datauppsättningstyper har lagts till:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Följande kopierar källtyper har lagts till:     
  * [WebSource (websource)](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Version 4.4.0
### <a name="feature-additions"></a>Funktionstillägg
* Följande länkade tjänsttyp har lagts till som datakällor och sänkor för kopieringsaktiviteter:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Se [Azure Storage SAS Linked Service](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) för begreppsmässig information och exempel.

## <a name="version-430"></a>Version 4.3.0
### <a name="feature-additions"></a>Funktionstillägg
* Följande länkade tjänsttyper har lagts till som datakällor för kopieringsaktiviteter:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Se [Flytta data från HDFS med Data Factory](data-factory-hdfs-connector.md) för begreppsmässig information och exempel.
  * [PåPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Se [Flytta data från ODBC-datalager med Hjälp av Azure Data Factory](data-factory-odbc-connector.md) för begreppsmässig information och exempel.

## <a name="version-420"></a>Version 4.2.0
### <a name="feature-additions"></a>Funktionstillägg
* Följande nya aktivitetstyp har lagts till: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Mer information om aktiviteten finns i [Uppdatera Azure ML-modeller med hjälp av uppdateringsresursaktiviteten](data-factory-azure-ml-batch-execution-activity.md).
* En ny valfri [egenskapsuppdateringResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) har lagts till i [klassen AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [Egenskaperna LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) och [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) har lagts till i klassen [DataFactoryManagementClient.](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx)
* Tillåt konfiguration av tidsgränsen för klientanrop till tjänsten Data Factory.

## <a name="version-410"></a>Version 4.1.0
### <a name="feature-additions"></a>Funktionstillägg
* Följande länkade tjänsttyper har lagts till:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Följande aktivitetstyper har lagts till:
  * [DataLakeAnalyticsUSQLAktivitet](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Följande datauppsättningstyper har lagts till:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Följande käll- och handfatstyper för kopieringsaktivitet har lagts till:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Version 4.0.1
### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
Följande klasser har bytt namn. De nya namnen var de ursprungliga namnen på klasser innan 4.0.0 release.

| Namn i 4.0.0 | Namn i 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset (Orakl) |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Version 4.0.0
### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
* Följande klasser/gränssnitt har bytt namn.

| Gammalt namn | Nytt namn |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabell |[Datamängd](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TabellEgenskaper |[DatasetEgenskaper](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TabellFelegenskaper |[DatasetTypeEgenskaper](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TabellCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **Listmetoderna** returnerar växlingsresultat nu. Om svaret innehåller en icke-tom **NextLink-egenskap** måste klientprogrammet fortsätta att hämta nästa sida tills alla sidor returneras.  Här är ett exempel:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **Lista** pipeline API returnerar bara sammanfattningen av en pipeline i stället för fullständig information. Aktiviteter i en pipelinesammanfattning innehåller till exempel bara namn och typ.

### <a name="feature-additions"></a>Funktionstillägg
* Klassen [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) stöder två nya egenskaper, **SliceIdentifierColumnName** och **SqlWriterCleanupScript**, för att stödja idempotent kopia till Azure SQL Data Warehouse. Mer information om dessa egenskaper finns i artikeln [Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md)
* Vi stöder nu att köra lagrad procedur mot Azure SQL Database och Azure SQL Data Warehouse källor som en del av kopieringsaktiviteten. [Klasserna SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) och [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) har följande egenskaper: **SqlReaderStoredProcedureName** och **StoredProcedureParameters**. Mer information om dessa egenskaper finns i artiklarna [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) och Azure SQL Data [Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) på Azure.com.  
