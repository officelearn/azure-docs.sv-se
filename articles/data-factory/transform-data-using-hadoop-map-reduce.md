---
title: Transformera data med Hadoop-MapReduce-aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du bearbetar data genom att köra Hadoop MapReduce program på ett Azure HDInsight-kluster från en Azure-datafabrik.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 7ba1c4e0f358a2168c06ab9172229525cd4c136c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016145"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformera data med Hadoop-MapReduce-aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](v1/data-factory-map-reduce.md)
> * [Aktuell version](transform-data-using-hadoop-map-reduce.md)

HDInsight MapReduce-aktivitet i en Datafabrik [pipeline](concepts-pipelines-activities.md) anropar MapReduce-program på [egna](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-kluster. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](transform-data.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter.

Om du är nybörjare på Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](introduction.md) och igenom självstudien: [Självstudie: Transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

Se [Pig](transform-data-using-hadoop-pig.md) och [Hive](transform-data-using-hadoop-hive.md) för information om hur du använder Pig/Hive skript på ett HDInsight-kluster från en pipeline med hjälp av HDInsight Pig och Hive-aktiviteter. 

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

| Egenskap           | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| namn              | Namn på aktiviteten                     | Ja      |
| beskrivning       | Text som beskriver vad aktiviteten används till | Nej       |
| typ              | Aktivitetstypen är HDinsightMapReduce för MapReduce-aktivitet | Ja      |
| linkedServiceName | Referens till HDInsight-kluster som är registrerad som en länkad tjänst i Datafabriken. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln. | Ja      |
| Klassnamn         | Namnet på klassen som ska köras         | Ja      |
| jarLinkedService  | Referens till en Azure Storage-länkade tjänst som används för att lagra Jar-filerna. Om du inte anger den här länkade tjänsten, används den Azure Storage länkade tjänsten definieras i den länkade tjänsten HDInsight. | Nej       |
| jarFilePath       | Ange sökvägen till Jar-filerna som lagras i Azure Storage som refereras till av jarLinkedService. Filnamnet är skiftlägeskänsligt. | Ja      |
| jarlibs           | Sträng matris med sökvägen till Jar-DLL-fil som refereras av jobbet som lagras i Azure Storage som definierats i jarLinkedService. Filnamnet är skiftlägeskänsligt. | Nej       |
| getDebugInfo      | Anger om filerna kopieras till Azure Storage används av HDInsight-kluster (eller) anges av jarLinkedService. Tillåtna värden: Ingen alltid kan eller inte. Standardvärde: Ingen. | Nej       |
| argument         | Anger en matris med argumenten för ett Hadoop-jobb. Argumenten skickas till varje aktivitet som kommandoradsargument. | Nej       |
| definierar           | Ange parametrar som nyckel/värde-par för refererar till Hive-skript. | Nej       |



## <a name="example"></a>Exempel
Du kan använda HDInsight MapReduce-aktivitet för att köra alla MapReduce jar-filen på ett HDInsight-kluster. I följande exempel JSON-definition av en pipeline, har aktivitet för HDInsight konfigurerats för att köra en Mahout JAR-fil.

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
Du kan ange några argument för MapReduce-program i den **argument** avsnittet. Vid körning, som du ser några extra argument (till exempel: mapreduce.job.tags) från MapReduce-ramverket. Överväg att använda både alternativet och värdet som argument som visas i följande exempel för att skilja dina argument med MapReduce-argument (- s – indata,--utdata osv., är alternativen följt av deras värden).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som beskriver hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Piggningsåtgärd](transform-data-using-hadoop-pig.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning-batchkörningsaktivitet](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
