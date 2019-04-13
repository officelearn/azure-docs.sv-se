---
title: Transformera data med Hadoop Streaming Activity - Azure | Microsoft Docs
description: Lär dig hur du kan använda Hadoop Streaming Activity i Azure data factory för att omvandla data genom att köra Hadoop Streaming program på ett på-begäran/your own HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: dd00c0a2998009ce6c39ca19abb25a2548682cee
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523230"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformera data med Hadoop Streaming Activity i Azure Data Factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Piggningsåtgärd](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Transformera data med Hadoop-strömmande aktiviteten i Data Factory](../transform-data-using-hadoop-streaming.md).


Du kan använda HDInsightStreamingActivity aktiviteten anropa en Hadoop Streaming-jobbet från en Azure Data Factory-pipeline. Följande JSON-kodfragmentet visar syntaxen för att använda HDInsightStreamingActivity i en pipeline-JSON-fil. 

HDInsight-Strömningsaktivitet i en Datafabrik [pipeline](data-factory-create-pipelines.md) Hadoop Streaming program körs på [egna](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserat HDInsight-kluster. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter.

> [!NOTE] 
> Om du är nybörjare på Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](data-factory-introduction.md) och igenom självstudien: [Skapa din första datapipeline](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln. 

## <a name="json-sample"></a>JSON-exempel
HDInsight-klustret fylls automatiskt med exempel programmen (wc.exe och cat.exe) och data (davinci.txt). Som standard är namnet på behållaren som används av HDInsight-klustret namnet på själva klustret. Till exempel om klusternamnet är myhdicluster, blir namnet på blob-behållare som är associerade myhdicluster. 

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

1. Ange den **linkedServiceName** till namnet på den länkade tjänst som pekar på ditt HDInsight-kluster som strömmande mapreduce-jobb körs.
2. Ange typ av aktiviteten **HDInsightStreaming**.
3. För den **mapper** egenskapen, ange namnet på mapper körbara. I det här exemplet är cat.exe mapper körbara.
4. För den **reducer** egenskapen, ange namnet på reducer körbara. I det här exemplet är wc.exe reducer körbara.
5. För den **inkommande** typegenskapen, ange indatafilen (inklusive platsen) för mappningen. I det här exemplet: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample är blob-behållaren, exempel/data/Gutenberg är mappen och davinci.txt är blob.
6. För den **utdata** typegenskapen, ange utdatafilen (inklusive platsen) för reducer. Utdata från Hadoop Streaming-jobbet skrivs till den angivna platsen för den här egenskapen.
7. I den **filePaths** anger sökvägar för mapper och reducer körbara filer. I det här exemplet: ”adfsample/example/apps/wc.exe” adfsample är blobbehållaren exempelappar/är mappen och wc.exe är den körbara filen.
8. För den **fileLinkedService** egenskapen, ange den länkade Azure Storage-tjänst som representerar Azure-lagring som innehåller de filer som anges i avsnittet filePaths.
9. För den **argument** egenskapen, ange argumenten för det direktuppspelade jobbet.
10. Den **getDebugInfo** egenskapen är ett valfritt element. När den är inställd på fel laddas i loggarna ned endast vid fel. När den är inställd på alltid hämtas alltid loggar oavsett körningsstatusen.

> [!NOTE]
> Som du ser i det här exemplet kan du ange en utdatauppsättning för Hadoop Streaming Activity för den **matar ut** egenskapen. Den här datauppsättningen är bara en dummy datauppsättning som krävs för att driva pipeline-schema. Du behöver inte ange några datauppsättningen för indata för aktiviteten för de **indata** egenskapen.  
> 
> 

## <a name="example"></a>Exempel
Pipelinen i den här genomgången kör ordräkning strömmande Map/Reduce programmet på Azure HDInsight-kluster. 

### <a name="linked-services"></a>Länkade tjänster
#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Först skapar du en länkad tjänst för att länka Azure Storage som används av Azure HDInsight-kluster till Azure data factory. Om du kopiera och klistra in följande kod, Glöm inte att ersätta kontonamnet och kontonyckeln med namnet och nyckeln för Azure Storage. 

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

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight-länkad tjänst
Därefter skapar du en länkad tjänst för att länka ditt Azure HDInsight-kluster till Azure data factory. Om du kopiera och klistra in följande kod, Ersätt HDInsight-klusternamnet med namnet på ditt HDInsight-kluster och ändra värden för användare och lösenord. 

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
#### <a name="output-dataset"></a>Datauppsättningen för utdata
Pipelinen i det här exemplet tar inte alla indata. Du anger en datauppsättning för utdata för HDInsight-Strömningsaktivitet. Den här datauppsättningen är bara en dummy datauppsättning som krävs för att driva pipeline-schema. 

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
Pipelinen i det här exemplet har en aktivitet som är av typen: **HDInsightStreaming**. 

HDInsight-klustret fylls automatiskt med exempel programmen (wc.exe och cat.exe) och data (davinci.txt). Som standard är namnet på behållaren som används av HDInsight-klustret namnet på själva klustret. Till exempel om klusternamnet är myhdicluster, blir namnet på blob-behållare som är associerade myhdicluster.  

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
* [Piggningsåtgärd](data-factory-pig-activity.md)
* [MapReduce-aktivitet](data-factory-map-reduce.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

