---
title: Data Factory – .NET API-Ändringslogg | Microsoft Docs
description: Beskriver ändringar, funktionen tillägg, felkorrigeringar osv... i en specifik version av .NET-API för Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 863f3500c84eeab1c3dac19141cd334fc6961694
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567256"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – .NET API-Ändringslogg
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Den här artikeln innehåller information om ändringar till Azure Data Factory SDK i en specifik version. Du hittar det senaste NuGet-paketet för Azure Data Factory [här](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Version 4.11.0
Funktionen tillägg:

* Följande typer av den länkade tjänsten har lagts till:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Följande typer av datauppsättningen har lagts till:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Följande typer av kopiera datakällor har lagts till:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Version 4.10.0
* Följande valfria egenskaper har lagts till TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [treatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Följande typer av den länkade tjänsten har lagts till:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Följande typer av datauppsättningen har lagts till:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Följande typer av kopiera datakällor har lagts till:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Lägg till [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) AzureMLBatchExecutionActivity egenskap
  * Aktivera skicka flera web service indata till ett Azure Machine Learning-experiment

## <a name="version-491"></a>Version 4.9.1
### <a name="bug-fix"></a>Felkorrigering
* Avverka WebApi-baserad autentisering för [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Version 4.9.0
### <a name="feature-additions"></a>Funktionen tillägg
* Lägg till [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) och [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) egenskaper så att CopyActivity. Se [mellanlagrad kopiering](data-factory-copy-activity-performance.md#staged-copy) information om funktionen.

### <a name="bug-fix"></a>Felkorrigering
* Introducerar en överlagring för [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metod som tar en [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) instans.
* Markera [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) och [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) som valfria i CopySink.

## <a name="version-480"></a>Version 4.8.0
### <a name="feature-additions"></a>Funktionen tillägg
* Följande valfria egenskaper har lagts till kopia aktivitetstyp att aktivera justering av kopieringen bättre prestanda:
  * [parallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Version 4.7.0
### <a name="feature-additions"></a>Funktionen tillägg
* Lagt till nya StorageFormat typen [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) Skriv för att kopiera filer till optimerade raden (ORC) kolumnformat.
* Lägg till [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) och PolyBaseSettings egenskaper som SqlDWSink.
  * Kan du använda PolyBase för att kopiera data till SQL Data Warehouse.

## <a name="version-461"></a>Version 4.6.1
### <a name="bug-fixes"></a>Felkorrigeringar
* Åtgärdar HTTP-begäran för att visa aktivitetsfönster.
  * Tar bort resursgruppens namn och datafabriksnamnet från nyttolasten i begäran.

## <a name="version-460"></a>Version 4.6.0
### <a name="feature-additions"></a>Funktionen tillägg
* Följande egenskaper har lagts till [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Datauppsättningar](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Följande egenskaper har lagts till [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Lägger till nya [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) typ [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) Skriv för att definiera datauppsättningar vars data är i JSON-format.

## <a name="version-450"></a>Version 4.5.0
### <a name="feature-additions"></a>Funktionen tillägg
* Lagt till [Liståtgärder för aktivitetsfönstret](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Har lagts till metoder för att hämta aktivitetsfönster med filter baserat på entitetstyper (det vill säga datafabriker, datauppsättningar, pipeliner och aktiviteter).
* Följande typer av den länkade tjänsten har lagts till:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Följande typer av datauppsättningen har lagts till:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Följande typer av kopiera datakällor har lagts till:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Version 4.4.0
### <a name="feature-additions"></a>Funktionen tillägg
* Den länkade tjänsttypen har lagts till som datakällor och egenskaperna för kopieringsaktiviteter:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Se [Azure SAS länkad lagringstjänst](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) konceptuell information och exempel.

## <a name="version-430"></a>Version 4.3.0
### <a name="feature-additions"></a>Funktionen tillägg
* Följande länkade tjänsten typer haven har lagts till som datakällor för kopieringsaktiviteter:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Se [flytta data från HDFS med Data Factory](data-factory-hdfs-connector.md) konceptuell information och exempel.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Se [flytta data från ODBC-datalager med Azure Data Factory](data-factory-odbc-connector.md) konceptuell information och exempel.

## <a name="version-420"></a>Version 4.2.0
### <a name="feature-additions"></a>Funktionen tillägg
* Följande nya aktivitetstyp har lagts till: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Mer information om aktiviteten finns i [uppdaterar Azure ML-modeller med hjälp av den Uppdateringsresursaktivitet](data-factory-azure-ml-batch-execution-activity.md).
* En ny valfri egenskap [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) har lagts till i [AzureMLLinkedService klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) och [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) egenskaper har lagts till i [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) klass.
* Att konfigurera inställningarna för timeout för klientanrop till Data Factory-tjänsten.

## <a name="version-410"></a>Version 4.1.0
### <a name="feature-additions"></a>Funktionen tillägg
* Följande typer av den länkade tjänsten har lagts till:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Följande aktivitetstyper av har lagts till:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Följande typer av datauppsättningen har lagts till:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Följande typer av källa och mottagare för Kopieringsaktiviteten har lagts till:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Version 4.0.1
### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
Följande klasser har bytt namn. De nya namnen var de ursprungliga namnen på klasser innan 4.0.0 släpper.

| Namn i 4.0.0 | Namn i 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Version 4.0.0
### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
* Följande klasser/gränssnitt har bytt namn.

| Gammalt namn | Nytt namn |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabell |[Datauppsättning](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* Den **lista** metoder returnerar växlade resultat nu. Om svaret innehåller en icke-tom **NextLink** egenskapen klientprogrammet måste du fortsätta att hämta nästa sida tills alla sidor som returneras.  Här är ett exempel:

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
* **Lista** pipeline API returnerar endast en sammanfattning av en pipeline i stället för fullständig information. Aktiviteter i en pipeline-sammanfattning innehåller till exempel endast namn och typ.

### <a name="feature-additions"></a>Funktionen tillägg
* Den [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) klassen stöder två nya egenskaper **SliceIdentifierColumnName** och **SqlWriterCleanupScript**att stödja idempotenta kopiera till Azure SQL-Data Datalager. Se den [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) nedan för information om dessa egenskaper.
* Nu kan du köra lagrade procedurer mot Azure SQL Database och Azure SQL Data Warehouse källor som en del av Kopieringsaktiviteten. Den [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) och [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) klasser har följande egenskaper: **SqlReaderStoredProcedureName** och **StoredProcedureParameters**. Se den [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) och [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) artiklar på Azure.com finns information om de här egenskaperna.  
