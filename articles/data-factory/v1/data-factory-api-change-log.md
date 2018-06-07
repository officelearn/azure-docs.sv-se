---
title: Data Factory - .NET API ändringsloggen | Microsoft Docs
description: 'Beskriver viktiga förändringar, funktionen tillägg, felkorrigeringar etc... i en viss version av .NET-API: et för Azure Data Factory.'
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: c5f47379072f4e5d15ffd96c5e45a23d10fff187
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620256"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - ändringsloggen för .NET-API
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). 

Den här artikeln innehåller information om ändringar till Azure Data Factory SDK i en viss version. Du hittar det senaste NuGet-paketet för Azure Data Factory [här](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Version 4.11.0
Funktionen tillägg:

* På följande länkade tjänsttyper har lagts till:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Följande typer av datamängden har lagts till:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Följande typer av kopiera källan har lagts till:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Version 4.10.0
* Följande valfria egenskaper har lagts till TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* På följande länkade tjänsttyper har lagts till:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Följande typer av datamängden har lagts till:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Följande typer av kopiera källan har lagts till:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Lägg till [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) egenskapen AzureMLBatchExecutionActivity
  * Aktivera skicka flera web service indata till ett Azure Machine Learning-experiment

## <a name="version-491"></a>Version 4.9.1
### <a name="bug-fix"></a>Buggfix
* Föråldrad WebApi-baserad autentisering för [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Version 4.9.0
### <a name="feature-additions"></a>Funktionen tillägg
* Lägg till [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) och [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) egenskaper för CopyActivity. Se [mellanlagrad kopiera](data-factory-copy-activity-performance.md#staged-copy) mer information om funktionen.

### <a name="bug-fix"></a>Buggfix
* Introducerar en överlagring av [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metod som tar en [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) instans.
* Markera [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) och [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) som valfria i CopySink.

## <a name="version-480"></a>Version 4.8.0
### <a name="feature-additions"></a>Funktionen tillägg
* Följande valfria egenskaper har lagts till kopia aktivitetstyp att aktivera justering av kopiera prestanda:
  * [parallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [cloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Version 4.7.0
### <a name="feature-additions"></a>Funktionen tillägg
* Lägga till ny typ av StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) typ att kopiera filer i optimerad raden (ORC) kolumnformat.
* Lägg till [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) och PolyBaseSettings egenskaper i SqlDWSink.
  * Kan du använda PolyBase för att kopiera data till SQL Data Warehouse.

## <a name="version-461"></a>Version 4.6.1
### <a name="bug-fixes"></a>Felkorrigeringar
* Åtgärdar HTTP-begäran för att lista aktivitet windows.
  * Tar bort resursgruppens namn och data factory-namnet från nyttolasten i begäran.

## <a name="version-460"></a>Version 4.6.0
### <a name="feature-additions"></a>Funktionen tillägg
* Följande egenskaper har lagts till [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Datauppsättningar](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Följande egenskaper har lagts till [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Lägga till nya [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) typen [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) typ att definiera datauppsättningarna vars data är i JSON-format.

## <a name="version-450"></a>Version 4.5.0
### <a name="feature-additions"></a>Funktionen tillägg
* Lägga till [lista över åtgärder för aktivitetsfönstret](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Tillagda metoder för att hämta aktivitet windows med filtreras baserat på entitetstyper (det vill säga datafabriker, datauppsättningar, rörledningar och aktiviteter).
* På följande länkade tjänsttyper har lagts till:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Följande typer av datamängden har lagts till:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Följande typer av kopiera källan har lagts till:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Version 4.4.0
### <a name="feature-additions"></a>Funktionen tillägg
* Den länkade tjänsttypen har lagts till som datakällor och egenskaperna för kopiera aktiviteter:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Se [Azure SAS länkade lagringstjänsten](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) konceptuell information och exempel.

## <a name="version-430"></a>Version 4.3.0
### <a name="feature-additions"></a>Funktionen tillägg
* På följande länkade tjänsten typer haven har lagts till som datakällor för kopiera aktiviteter:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Se [flytta data från HDFS med hjälp av Data Factory](data-factory-hdfs-connector.md) konceptuell information och exempel.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Se [flytta data från ODBC datalager med Azure Data Factory](data-factory-odbc-connector.md) konceptuell information och exempel.

## <a name="version-420"></a>Version 4.2.0
### <a name="feature-additions"></a>Funktionen tillägg
* Följande ny aktivitetstyp har lagts till: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Mer information om aktiviteten finns [uppdaterar Azure ML-modeller med hjälp av den Uppdateringsresursaktivitet](data-factory-azure-ml-batch-execution-activity.md).
* En ny valfri egenskap [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) har lagts till i [AzureMLLinkedService klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) och [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) egenskaper har lagts till i [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) klass.
* Att konfigurera inställningarna för timeout för klientanrop till Data Factory-tjänsten.

## <a name="version-410"></a>Version 4.1.0
### <a name="feature-additions"></a>Funktionen tillägg
* På följande länkade tjänsttyper har lagts till:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Följande aktivitetstyper av har lagts till:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Följande typer av datamängden har lagts till:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Följande typer av källa och mottagare för Kopieringsaktiviteten har lagts till:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Version 4.0.1
### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
Följande klasser har bytt namn. De nya namn var de ursprungliga namnen på klasser innan 4.0.0 släpper.

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
* Följande klasser/gränssnitt har ändrats.

| Gamla namnet | Nytt namn |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabell |[DataSet](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* Den **lista** metoder returnerar växlingsbara resultat nu. Om svaret innehåller ett icke-tomt **NextLink** egenskapen klientprogrammet måste fortsätta hämta nästa sida tills alla sidor som returneras.  Här är ett exempel:

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
* **Lista** pipeline API returnerar bara en sammanfattning av en pipeline i stället för fullständig information. Till exempel innehålla aktiviteter i en pipeline-sammanfattning endast namn och typ.

### <a name="feature-additions"></a>Funktionen tillägg
* Den [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) klassen stöder två nya egenskaper **SliceIdentifierColumnName** och **SqlWriterCleanupScript**att stödja idempotent kopiera till Azure SQL Data Warehouse. Finns det [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) artikeln för information om dessa egenskaper.
* Vi stöder nu Kör lagrad procedur mot Azure SQL Database och Azure SQL Data Warehouse källor som en del av aktiviteten kopia. Den [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) och [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) klasser har följande egenskaper: **SqlReaderStoredProcedureName** och **StoredProcedureParameters**. Finns det [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) och [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) artiklar på Azure.com mer information om dessa egenskaper.  
