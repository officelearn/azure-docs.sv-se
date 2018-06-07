---
title: Transformera data med Hadoop Streaming Activity - Azure | Microsoft Docs
description: Lär dig hur du kan använda Hadoop Streaming Activity i ett Azure data factory för att omvandla data med Hadoop Streaming program som körs på en på-begäran/din egen HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e405f99388f699b3f1e88b9e84f52f9a4380228c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621980"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformera data med Hadoop Streaming Activity i Azure Data Factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Pig-aktivitet](data-factory-pig-activity.md)
> * [MapReduce Activity](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [Anpassad aktivitet för .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Transformera data med Hadoop-strömning aktivitet i Data Factory version 2](../transform-data-using-hadoop-streaming.md).


Du kan använda HDInsightStreamingActivity aktiviteten anropa en Hadoop Streaming job från ett Azure Data Factory-pipelinen. Följande JSON-utdrag visar syntaxen för HDInsightStreamingActivity i en pipeline-JSON-fil. 

HDInsight Streaming Activity i en Datafabrik [pipeline](data-factory-create-pipelines.md) Hadoop Streaming program körs på [egna](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserade HDInsight-kluster. Den här artikeln bygger på den [data transformation aktiviteter](data-factory-data-transformation-activities.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter.

> [!NOTE] 
> Om du har använt Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](data-factory-introduction.md) och gör kursen: [skapa din första pipeline data](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln. 

## <a name="json-sample"></a>JSON-exempel
HDInsight-klustret fylls automatiskt med exempel program (wc.exe och cat.exe) och (davinci.txt). Som standard är namnet på behållaren som används av HDInsight-klustret namn för själva klustret. Om klusternamnet är myhdicluster, är namnet på blobbehållare som är kopplad till exempel myhdicluster. 

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

1. Ange den **linkedServiceName** till namnet på den länkade tjänst som pekar på ditt HDInsight-kluster som strömmande mapreduce-jobbet körs.
2. Ange vilken typ av aktivitet till **HDInsightStreaming**.
3. För den **mapper** egenskap, ange namnet på mapper körbara. I det här exemplet är cat.exe mapper körbara.
4. För den **reducer** egenskap, ange namnet på reducer körbara. I det här exemplet är wc.exe reducer körbara.
5. För den **inkommande** egenskapen type, ange indatafilen (inklusive platsen) för mapparen. Exempel ”: wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt”: adfsample är blobbehållare, exempel/data/Gutenberg är mappen och davinci.txt är blob.
6. För den **utdata** egenskapen type, ange utdatafilen (inklusive platsen) för reducer. Utdata för direktuppspelning av Hadoop-jobb skrivs till den plats som anges för den här egenskapen.
7. I den **filePaths** ange sökvägar för mappning och reducer körbara filer. Exempel: ”adfsample/example/apps/wc.exe” adfsample är blobbehållare, exempel/appar är mappen och wc.exe är den körbara filen.
8. För den **fileLinkedService** egenskap, ange länkad Azure Storage-tjänst som representerar den Azure-lagring som innehåller de filer som anges i avsnittet filePaths.
9. För den **argument** egenskap, ange argument för direktuppspelningsjobbet.
10. Den **getDebugInfo** egenskapen är ett valfritt element. När den är inställd på fel laddas i loggarna ned endast vid fel. När den är inställd på alltid laddas alltid loggar oavsett körningsstatusen.

> [!NOTE]
> Som du ser i exemplet kan du ange en datamängd för utdata för Hadoop Streaming Activity för den **matar ut** egenskapen. Den här datauppsättningen är bara en dummy datamängd som krävs för att driva pipeline-schemat. Du behöver inte ange några inkommande dataset för aktiviteten för den **indata** egenskapen.  
> 
> 

## <a name="example"></a>Exempel
Ordräkning strömmande kartan/minska programmet körs i pipeline i den här genomgången på Azure HDInsight-klustret. 

### <a name="linked-services"></a>Länkade tjänster
#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Först skapar du en länkad tjänst om du vill länka Azure Storage som används av Azure HDInsight-kluster till Azure data factory. Om du kopiera och klistra in följande kod, Glöm inte att ersätta kontonamn och kontonyckel med namn och nyckel för Azure Storage. 

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
Därefter skapar du en länkad tjänst om du vill länka ditt Azure HDInsight-kluster till Azure data factory. Om du kopiera och klistra in följande kod, Ersätt HDInsight-klustrets namn med namnet på ditt HDInsight-kluster och ändra värdena för användarens namn och lösenord. 

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
#### <a name="output-dataset"></a>Datamängd för utdata
Pipeline i det här exemplet tar inte alla indata. Du kan ange en datamängd för utdata för aktiviteten strömning HDInsight. Den här datauppsättningen är bara en dummy datamängd som krävs för att driva pipeline-schemat. 

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
Pipeline i det här exemplet har endast en aktivitet som är av typen: **HDInsightStreaming**. 

HDInsight-klustret fylls automatiskt med exempel program (wc.exe och cat.exe) och (davinci.txt). Som standard är namnet på behållaren som används av HDInsight-klustret namn för själva klustret. Om klusternamnet är myhdicluster, är namnet på blobbehållare som är kopplad till exempel myhdicluster.  

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
* [Pig-aktivitet](data-factory-pig-activity.md)
* [MapReduce Activity](data-factory-map-reduce.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

