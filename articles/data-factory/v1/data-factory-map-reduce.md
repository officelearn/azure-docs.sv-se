---
title: Anropa MapReduce Program från Azure Data Factory
description: Lär dig mer om att bearbeta data genom att köra MapReduce program på Azure HDInsight-kluster från en Azure data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6bddb3d5beca57852ef62361b278ec1fa8961f45
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621266"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Anropa MapReduce program från Data Factory
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
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Transformera data med MapReduce activity från Data Factory version 2](../transform-data-using-hadoop-map-reduce.md).


HDInsight MapReduce-aktiviteten i en Datafabrik [pipeline](data-factory-create-pipelines.md) MapReduce-program körs på [egna](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserade HDInsight-kluster. Den här artikeln bygger på den [data transformation aktiviteter](data-factory-data-transformation-activities.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter.

> [!NOTE] 
> Om du har använt Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](data-factory-introduction.md) och gör kursen: [skapa din första pipeline data](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln.  

## <a name="introduction"></a>Introduktion
En pipeline i ett Azure data factory bearbetar data i länkade storage-tjänster med hjälp av länkade beräknings-tjänster. Den innehåller en serie aktiviteter där varje aktivitet utför en specifik bearbetning. Den här artikeln beskriver hur du använder HDInsight MapReduce Activity.

Se [Pig](data-factory-pig-activity.md) och [Hive](data-factory-hive-activity.md) mer information om hur du kör Pig/Hive skript på en Windows/Linux-baserade HDInsight-kluster från en rörledning HDInsight Pig och Hive aktiviteter. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON för HDInsight MapReduce Activity
I JSON-definitionen för aktiviteten HDInsight: 

1. Ange den **typen** av den **aktiviteten** till **HDInsight**.
2. Ange namnet på klassen för **className** egenskapen.
3. Ange sökvägen till JAR-filen och filnamnet för **jarFilePath** egenskapen.
4. Ange den länkade tjänst som refererar till Azure Blob Storage som innehåller JAR-filen för **jarLinkedService** egenskapen.   
5. Ange några argument för MapReduce-program i den **argument** avsnitt. Vid körning kan du se några extra argument (till exempel: mapreduce.job.tags) från MapReduce-ramverket. Överväg att använda både alternativet och värde som argument som visas i följande exempel för att skilja dina argument med MapReduce-argument (- s,--indata--utdata osv., är en alternativ direkt följt av deras värden).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
Du kan använda HDInsight MapReduce aktiviteten för att köra alla MapReduce jar-filen på ett HDInsight-kluster. I följande exempel JSON-definitionen för en pipeline konfigureras aktiviteten HDInsight för att köra en Mahout JAR-fil.

## <a name="sample-on-github"></a>Exempel på GitHub
Du kan hämta ett exempel för att använda HDInsight MapReduce Activity från: [Data Factory exemplen på GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Ordräkning när programmet körs
Pipeline i det här exemplet körs ordräkning kartan/minska programmet på Azure HDInsight-kluster.   

### <a name="linked-services"></a>Länkade tjänster
Först skapar du en länkad tjänst om du vill länka Azure Storage som används av Azure HDInsight-kluster till Azure data factory. Om du kopiera och klistra in följande kod, Glöm inte att ersätta **kontonamn** och **kontonyckel** med namn och nyckel för Azure Storage. 

#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst

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
Därefter skapar du en länkad tjänst om du vill länka ditt Azure HDInsight-kluster till Azure data factory. Om du kopiera och klistra in följande kod, ersätter **HDInsight-klustrets namn** med namnet på ditt HDInsight-kluster och ändra användarens namn och lösenord värden.   

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
Pipeline i det här exemplet tar inte alla indata. Du kan ange en datamängd för utdata för HDInsight MapReduce Activity. Den här datauppsättningen är bara en dummy datamängd som krävs för att driva pipeline-schemat.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Pipeline i det här exemplet har endast en aktivitet som är av typen: HDInsightMapReduce. Några viktiga egenskaper i JSON är: 

| Egenskap  | Anteckningar |
|:--- |:--- |
| typ |Typen måste anges till **HDInsightMapReduce**. |
| Klassnamn |Namnet på klassen är: **wordcount** |
| jarFilePath |Sökvägen till jar-filen som innehåller klassen. Om du kopiera och klistra in följande kod, Glöm inte att ändra namnet på klustret. |
| jarLinkedService |Azure Storage länkade tjänst som innehåller jar-filen. Den här länkade tjänsten refererar till den lagring som är associerad med HDInsight-klustret. |
| Argument |Wordcount-programmet tar två argument, indata och utdata. Indatafilen är davinci.txt-fil. |
| frekvens/intervall |Värdena för dessa egenskaper matchar datamängd för utdata. |
| linkedServiceName |refererar till länkad HDInsight-tjänsten som du har skapat tidigare. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Köra Spark-program
Du kan använda MapReduce-aktiviteten för att köra Spark-program i ditt HDInsight Spark-kluster. Mer information finns i [Invoke Spark programs from Azure Data Factory](data-factory-spark.md) (Anropa Spark-program från Azure Data Factory).  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

## <a name="see-also"></a>Se även
* [Hive-aktivitet](data-factory-hive-activity.md)
* [Pig-aktivitet](data-factory-pig-activity.md)
* [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

