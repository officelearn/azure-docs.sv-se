---
title: Omvandla data med hjälp av Hadoop Streaming Activity - Azure
description: Lär dig hur du kan använda Hadoop Streaming Activity i en Azure-datafabrik för att omvandla data genom att köra Hadoop Streaming-program på ett on-demand/ditt eget HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a7f07365da699a40f5b51917104a68a62affa3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703372"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Omvandla data med hjälp av Hadoop Streaming Activity i Azure Data Factory
> [!div class="op_single_selector" title1="Omvandlingsaktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Grisaktivitet](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [transformera data med Hjälp av Hadoop-direktuppspelningsaktivitet i Data Factory](../transform-data-using-hadoop-streaming.md).


Du kan använda HDInsightStreamingActivity Activity anropa ett Hadoop Streaming-jobb från en Azure Data Factory-pipeline. Följande JSON-kodavsnitt visar syntaxen för att använda HDInsightStreamingActivity i en pipeline-JSON-fil. 

HDInsight Streaming Activity i en Pipeline för [datafabrik](data-factory-create-pipelines.md) kör Hadoop Streaming-program på [ditt eget](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [on-demand](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserade HDInsight-kluster. Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](data-factory-data-transformation-activities.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.

> [!NOTE] 
> Om du inte har gjort det tidigare i Azure Data Factory läser du [in introduktion till Azure Data Factory](data-factory-introduction.md) och gör självstudien: Skapa din första [datapipeline](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln. 

## <a name="json-sample"></a>JSON-exempel
HDInsight-klustret fylls automatiskt i med exempelprogram (wc.exe och cat.exe) och data (davinci.txt). Som standard är namnet på behållaren som används av HDInsight-klustret namnet på själva klustret. Om klusternamnet till exempel är myhdicluster, skulle namnet på blob-behållaren som är associerad vara myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Observera följande punkter:

1. Ange **det länkadeServiceName** till namnet på den länkade tjänst som pekar på ditt HDInsight-kluster där jobbet för direktuppspelning mapreduce körs.
2. Ange typen av aktivitet till **HDInsightStreaming**.
3. För **egenskapen mapper** anger du namnet på den körbara mapparen. I exemplet är cat.exe den körbara mapparen.
4. För egenskapen **reducer** anger du namnet på den körbara reduceren. I exemplet är wc.exe den körbara reduceren.
5. För egenskapen **indatatyp** anger du indatafilen (inklusive platsen) för mappern. I exemplet: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample är blob-behållaren, exempel/data/Gutenberg är mappen och davinci.txt är bloben.
6. För egenskapen **utdatatyp** anger du utdatafilen (inklusive platsen) för reduceraren. Utdata för jobbet Hadoop Streaming skrivs till den plats som angetts för den här egenskapen.
7. I avsnittet **filePaths** anger du sökvägarna för mapper- och reducer-körbara filer. I exemplet "adfsample/example/apps/wc.exe" är adfsample blob-behållaren, exempel/appar mappen och wc.exe är den körbara filen.
8. För egenskapen **fileLinkedService** anger du den Azure Storage-länkade tjänsten som representerar Azure-lagringen som innehåller filerna som anges i avsnittet filePaths.
9. För **egenskapen argument** anger du argumenten för direktuppspelningsjobbet.
10. Egenskapen **getDebugInfo** är ett valfritt element. När den är inställd på Fel hämtas loggarna endast vid fel. När den är inställd på Alltid hämtas loggar alltid oavsett körningsstatus.

> [!NOTE]
> Som visas i exemplet anger du en utdatauppsättning för hadoop-direktuppspelningsaktiviteten för egenskapen **outputs.** Den här datauppsättningen är bara en dummy-datauppsättning som krävs för att köra pipelineschemat. Du behöver inte ange någon indatauppsättning för aktiviteten för egenskapen **indata.**  
> 
> 

## <a name="example"></a>Exempel
Pipelinen i den här genomgången kör programmet För direktuppspelning av Word Count på ditt Azure HDInsight-kluster. 

### <a name="linked-services"></a>Länkade tjänster
#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Först skapar du en länkad tjänst för att länka Azure Storage som används av Azure HDInsight-klustret till Azure-datafabriken. Om du kopierar/klistrar in följande kod, glöm inte att ersätta kontonamn och kontonyckel med namnet och nyckeln för din Azure Storage. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight länkad tjänst
Därefter skapar du en länkad tjänst för att länka ditt Azure HDInsight-kluster till Azure-datafabriken. Om du kopierar/klistrar in följande kod ersätter du HDInsight-klusternamnet med namnet på HDInsight-klustret och ändrar värden för användarnamn och lösenord. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Datauppsättningar
#### <a name="output-dataset"></a>Utdatauppsättning
Pipelinen i det här exemplet tar inga indata. Du anger en utdatauppsättning för HDInsight Streaming Activity. Den här datauppsättningen är bara en dummy-datauppsättning som krävs för att köra pipelineschemat. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Pipelinen i det här exemplet har bara en aktivitet av typen **HDInsightStreaming**. 

HDInsight-klustret fylls automatiskt i med exempelprogram (wc.exe och cat.exe) och data (davinci.txt). Som standard är namnet på behållaren som används av HDInsight-klustret namnet på själva klustret. Om klusternamnet till exempel är myhdicluster, skulle namnet på blob-behållaren som är associerad vara myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Se även
* [Hive-aktivitet](data-factory-hive-activity.md)
* [Grisaktivitet](data-factory-pig-activity.md)
* [MapReduce-aktivitet](data-factory-map-reduce.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

