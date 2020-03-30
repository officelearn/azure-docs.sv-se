---
title: Anropa MapReduce-program från Azure Data Factory
description: Lär dig hur du bearbetar data genom att köra MapReduce-program på ett Azure HDInsight-kluster från en Azure-datafabrik.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 598a16d25ba375b984a966cba190181edbda3d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703153"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Anropa MapReduce-program från Data Factory
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
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [transformera data med MapReduce-aktivitet i Data Factory](../transform-data-using-hadoop-map-reduce.md).


HDInsight MapReduce-aktiviteten i en Pipeline [för](data-factory-create-pipelines.md) Data Factory kör MapReduce-program på [ditt eget](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller on-demand Windows/Linux-baserade HDInsight-kluster. [on-demand](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](data-factory-data-transformation-activities.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.

> [!NOTE] 
> Om du inte har gjort det tidigare i Azure Data Factory läser du [in introduktion till Azure Data Factory](data-factory-introduction.md) och gör självstudien: Skapa din första [datapipeline](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln.  

## <a name="introduction"></a>Introduktion
En pipeline i en Azure-datafabrik bearbetar data i länkade lagringstjänster med hjälp av länkade beräkningstjänster. Den innehåller en sekvens av aktiviteter där varje aktivitet utför en viss bearbetning. I den här artikeln beskrivs hur du använder HDInsight MapReduce Activity.

Se [Pig](data-factory-pig-activity.md) och [Hive](data-factory-hive-activity.md) för mer information om hur du kör Pig/Hive-skript på ett Windows/Linux-baserat HDInsight-kluster från en pipeline med hjälp av HDInsight Pig- och Hive-aktiviteter. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON för HDInsight MapReduce Aktivitet
I JSON-definitionen för HDInsight-aktiviteten: 

1. Ange **typen** av **aktivitet** till **HDInsight**.
2. Ange namnet på klassen för **className-egenskapen.**
3. Ange sökvägen till JAR-filen inklusive filnamnet för **jarFilePath-egenskapen.**
4. Ange den länkade tjänsten som refererar till Azure Blob Storage som innehåller JAR-filen för **jarLinkedService-egenskapen.**   
5. Ange eventuella argument för MapReduce-programmet i **argumentavsnittet.** Vid körning visas några extra argument (till exempel mapreduce.job.tags) från MapReduce-ramverket. Om du vill skilja dina argument med argumenten MapReduce kan du använda både alternativ och värde som argument som visas i följande exempel (-s, --input, --output etc., är alternativ som omedelbart följs av deras värden).

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
   Du kan använda HDInsight MapReduce-aktiviteten för att köra en MapReduce-jar-fil i ett HDInsight-kluster. I följande exempel på JSON-definition av en pipeline är HDInsight-aktiviteten konfigurerad för att köra en Mahout JAR-fil.

## <a name="sample-on-github"></a>Exempel på GitHub
Du kan hämta ett exempel för att använda HDInsight MapReduce-aktivitet från: [Data Factory Samples på GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Köra Word Count-programmet
Pipelinen i det här exemplet kör word count map/reduce-programmet i ditt Azure HDInsight-kluster.   

### <a name="linked-services"></a>Länkade tjänster
Först skapar du en länkad tjänst för att länka Azure Storage som används av Azure HDInsight-klustret till Azure-datafabriken. Om du kopierar/klistrar in följande kod, glöm inte att ersätta **kontonamn** och **kontonyckel** med namnet och nyckeln för din Azure Storage. 

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
Därefter skapar du en länkad tjänst för att länka ditt Azure HDInsight-kluster till Azure-datafabriken. Om du kopierar/klistrar in följande kod ersätter du **HDInsight-klusternamnet** med namnet på HDInsight-klustret och ändrar värden för användarnamn och lösenord.   

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
Pipelinen i det här exemplet tar inga indata. Du anger en utdatauppsättning för HDInsight MapReduce Activity. Den här datauppsättningen är bara en dummy-datauppsättning som krävs för att köra pipelineschemat.  

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
Pipelinen i det här exemplet har bara en aktivitet av typen HDInsightMapReduce. Några av de viktiga egenskaperna i JSON är: 

| Egenskap | Anteckningar |
|:--- |:--- |
| typ |Typen måste vara inställd på **HDInsightMapReduce**. |
| Classname |Klassens namn är: **wordcount** |
| jarFilePath |Sökväg till jar-filen som innehåller klassen. Om du kopierar/klistrar in följande kod ska du inte glömma att ändra namnet på klustret. |
| jarLinkedService |Azure Storage-länkad tjänst som innehåller jar-filen. Den här länkade tjänsten refererar till den lagring som är associerad med HDInsight-klustret. |
| Argument |Wordcount-programmet tar två argument, en indata och en utdata. Indatafilen är filen davinci.txt. |
| frekvens/intervall |Värdena för dessa egenskaper matchar utdatauppsättningen. |
| linkedServiceName |refererar till den HDInsight-länkade tjänst som du hade skapat tidigare. |

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

## <a name="run-spark-programs"></a>Kör Spark-program
Du kan använda MapReduce-aktiviteten för att köra Spark-program i ditt HDInsight Spark-kluster. Mer information finns i [Invoke Spark programs from Azure Data Factory](data-factory-spark.md) (Anropa Spark-program från Azure Data Factory).  

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Se även
* [Hive-aktivitet](data-factory-hive-activity.md)
* [Grisaktivitet](data-factory-pig-activity.md)
* [Hadoop streaming aktivitet](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

