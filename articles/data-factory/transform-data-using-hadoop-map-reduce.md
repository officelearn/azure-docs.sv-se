---
title: Transformera data med Hadoop-MapReduce activity i Azure Data Factory | Microsoft Docs
description: "Lär dig mer om att bearbeta data genom att köra Hadoop MapReduce program på Azure HDInsight-kluster från en Azure data factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: shengc
ms.openlocfilehash: b473ba03b8b700b3123f82343e59a1ed897c4189
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformera data med Hadoop-MapReduce activity i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-map-reduce.md)
> * [Version 2 – förhandsversion](transform-data-using-hadoop-map-reduce.md)


HDInsight MapReduce-aktiviteten i en Datafabrik [pipeline](concepts-pipelines-activities.md) anropar MapReduce-programmet på [egna](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-kluster. Den här artikeln bygger på den [data transformation aktiviteter](transform-data.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [MapReduce Activity i V1](v1/data-factory-map-reduce.md).


Om du har använt Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](introduction.md) och gör kursen: [Självstudier: Transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

Se [Pig](transform-data-using-hadoop-pig.md) och [Hive](transform-data-using-hadoop-hive.md) mer information om hur du kör Pig/Hive skript i ett HDInsight-kluster från en rörledning HDInsight Pig och Hive aktiviteter. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "jarlibs": "MyAzureStorage/jars/jar1",
        "getDebugInfo": "Failure",
        "arguments": [
          "-SampleHadoopJobArgument1"
        ],
        "defines": {
          "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Information om syntax

| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| namn              | Namnet på aktiviteten                     | Ja      |
| description       | Text som beskriver aktiviteten är det som används för | Nej       |
| typ              | För MapReduce Activity är aktivitetstypen HDinsightMapReduce | Ja      |
| linkedServiceName | Referens till HDInsight-klustret registreras som en länkad tjänst i Data Factory. Mer information om den här länkade tjänsten, se [Compute länkade tjänster](compute-linked-services.md) artikel. | Ja      |
| Klassnamn         | Namnet på klassen som ska köras         | Ja      |
| jarLinkedService  | Referens till en Azure Storage-länkade tjänst som används för att lagra Jar-filer. Om du inte anger den här länkade tjänsten används Azure länkade lagringstjänsten definieras i länkad HDInsight-tjänst. | Nej       |
| jarFilePath       | Ange sökvägen till Jar-filer som lagras i Azure Storage som anges av jarLinkedService. Filnamnet är skiftlägeskänslig. | Ja      |
| jarlibs           | Ange sökvägen till Jar-DLL-fil som refereras av jobb som lagras i Azure Storage som anges av jarLinkedService. Filnamnet är skiftlägeskänslig. | Nej       |
| getDebugInfo      | Anger om filerna kopieras till Azure Storage används av HDInsight-kluster (eller) anges av jarLinkedService. Tillåtna värden: None, alltid eller fel. Standardvärde: Ingen. | Nej       |
| Argument         | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som argument på kommandoraden för varje aktivitet. | Nej       |
| definierar           | Ange parametrar som nyckel/värde-par för refererar till i Hive-skript. | Nej       |



## <a name="example"></a>Exempel
Du kan använda HDInsight MapReduce aktiviteten för att köra alla MapReduce jar-filen på ett HDInsight-kluster. I följande exempel JSON-definitionen för en pipeline konfigureras aktiviteten HDInsight för att köra en Mahout JAR-fil.

```json   
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
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
    }
}
```
Du kan ange argument för MapReduce-program i den **argument** avsnitt. Vid körning kan du se några extra argument (till exempel: mapreduce.job.tags) från MapReduce-ramverket. Överväg att använda både alternativet och värde som argument som visas i följande exempel för att skilja dina argument med MapReduce-argument (- s,--indata--utdata osv., är en alternativ direkt följt av deras värden).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du Transformera data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Pig-aktivitet](transform-data-using-hadoop-pig.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution aktivitet](transform-data-using-machine-learning.md)
* [Aktiviteten lagrad procedur](transform-data-using-stored-procedure.md)
