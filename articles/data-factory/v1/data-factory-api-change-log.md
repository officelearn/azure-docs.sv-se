---
title: Data Factory-.NET API-ändrings logg
description: Beskriver hur du avbryter ändringar, funktions tillägg, fel korrigeringar och så vidare i en speciell version av .NET API för Azure Data Factory.
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
ms.openlocfilehash: b7936fcd1e4a629a813c4266920f6c34a15cf9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89438950"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory-.NET API-ändrings logg
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Den här artikeln innehåller information om ändringar i Azure Data Factory SDK i en angiven version. Du kan hitta det senaste NuGet-paketet för Azure Data Factory [här](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Version 4.11.0
Funktions tillägg:

* Följande länkade tjänst typer har lagts till:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Följande typer av data uppsättningar har lagts till:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Följande kopierings käll typer har lagts till:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Version 4.10.0
* Följande valfria egenskaper har lagts till i text rutan:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Följande länkade tjänst typer har lagts till:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Följande typer av data uppsättningar har lagts till:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Följande kopierings käll typer har lagts till:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Lägg till egenskapen [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) i AzureMLBatchExecutionActivity
  * Aktivera överföring av flera webb tjänst indata till ett Azure Machine Learning experiment

## <a name="version-491"></a>Version 4.9.1
### <a name="bug-fix"></a>Fel korrigering
* Föråldrad WebApi-baserad autentisering för [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Version 4.9.0
### <a name="feature-additions"></a>Funktions tillägg
* Lägg till [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) -och [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) -egenskaper till CopyActivity. Se [mellanlagrad kopia](data-factory-copy-activity-performance.md#staged-copy) för information om funktionen.

### <a name="bug-fix"></a>Fel korrigering
* Introducera en överlagring av [ActivityWindowOperationExtensions. list](https://msdn.microsoft.com/library/mt767915.aspx) -metoden, som tar en [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) -instans.
* Markera [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) och [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) som valfritt i CopySink.

## <a name="version-480"></a>Version 4.8.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande valfria egenskaper har lagts till för att kopiera aktivitets typen för att möjliggöra justering av kopierings prestanda:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Version 4.7.0
### <a name="feature-additions"></a>Funktions tillägg
* Ny StorageFormat typ av [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) har lagts till för att kopiera filer i optimerade rad kolumner (Orc).
* Lägg till [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) -och PolyBaseSettings-egenskaper till SqlDWSink.
  * Gör det möjligt att använda PolyBase för att kopiera data till Azure Synapse Analytics (tidigare SQL Data Warehouse).

## <a name="version-461"></a>Version 4.6.1
### <a name="bug-fixes"></a>Felkorrigeringar
* Korrigerar HTTP-begäran för List aktivitets fönster.
  * Tar bort resurs grupps namnet och data fabriks namnet från nytto lasten för begäran.

## <a name="version-460"></a>Version 4.6.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande egenskaper har lagts till i [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Datauppsättningar](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Följande egenskaper har lagts till i [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* En ny [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) typ av [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) har lagts till för att definiera data uppsättningar vars data är i JSON-format.

## <a name="version-450"></a>Version 4.5.0
### <a name="feature-additions"></a>Funktions tillägg
* Har lagt till [list åtgärder för aktivitets fönstret](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Metoder för att hämta aktivitets fönster med filter baserade på entitetstyper (det vill säga data fabriker, data uppsättningar, pipeliner och aktiviteter) har lagts till.
* Följande länkade tjänst typer har lagts till:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Följande typer av data uppsättningar har lagts till:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Följande kopierings käll typer har lagts till:     
  * [Webbkälla](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Version 4.4.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande länkad tjänst typ har lagts till som data källor och mottagare för kopierings aktiviteter:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Information och exempel finns i [Azure Storage SAS-länkade tjänster](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>Version 4.3.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande länkade tjänst typer jag lagts till som data källor för kopierings aktiviteter:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Se [Flytta data från HDFS med hjälp av Data Factory](data-factory-hdfs-connector.md) för konceptuell information och exempel.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Se [Flytta data från ODBC-datalager med hjälp av Azure Data Factory](data-factory-odbc-connector.md) för konceptuell information och exempel.

## <a name="version-420"></a>Version 4.2.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande nya aktivitets typ har lagts till: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Mer information om aktiviteten finns i [Uppdatera Azure ml-modeller med hjälp av aktiviteten uppdatera resurs](data-factory-azure-ml-batch-execution-activity.md).
* En ny valfri egenskap [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) har lagts till i [AzureMLLinkedService-klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* Egenskaperna [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) och [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) har lagts till i klassen [klassen datafactorymanagementclient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) .
* Tillåt konfiguration av tids gränser för klient anrop till tjänsten Data Factory.

## <a name="version-410"></a>Version 4.1.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande länkade tjänst typer har lagts till:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Följande aktivitets typer har lagts till:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Följande typer av data uppsättningar har lagts till:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Följande käll-och mottagar typer för kopierings aktiviteten har lagts till:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Version 4.0.1
### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
Följande klasser har bytt namn. De nya namnen är de ursprungliga namnen för klasser innan 4.0.0-versionen släpptes.

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
| Tabell |[Datamängd](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **List** metoderna returnerar växlade resultat nu. Om svaret innehåller en icke-tom **Nextlink** -egenskap måste klient programmet fortsätta att hämta nästa sida tills alla sidor har returnerats.  Här är ett exempel:

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
* **List** pipeline API returnerar bara sammanfattningen av en pipeline i stället för fullständig information. Till exempel innehåller aktiviteter i en pipeline-Sammanfattning endast namn och typ.

### <a name="feature-additions"></a>Funktions tillägg
* [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) -klassen stöder två nya egenskaper, **SliceIdentifierColumnName** och **SqlWriterCleanupScript**, för att stödja Idempotenta-kopiering till Azure Azure Synapse Analytics. Mer information om de här egenskaperna finns i artikeln om [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) .
* Vi har nu stöd för att köra lagrade procedurer mot Azure SQL Database och Azure Synapse Analytics-källor som en del av kopierings aktiviteten. [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) -och [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) -klasserna har följande egenskaper: **SqlReaderStoredProcedureName** och **StoredProcedureParameters**. Mer information om dessa egenskaper finns i artiklarna [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) och [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) på Azure.com.  
