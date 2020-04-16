---
title: Omvandla data med hadoop MapReduce-aktivitet
description: Lär dig hur du bearbetar data genom att köra Hadoop MapReduce-program på ett Azure HDInsight-kluster från en Azure-datafabrik.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: e3060f7e36f9e2696194da12c3c800555103d271
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418923"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Omvandla data med hadoop MapReduce-aktivitet i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-map-reduce.md)
> * [Aktuell version](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

HDInsight MapReduce-aktiviteten i en Data [Factory-pipeline](concepts-pipelines-activities.md) anropar MapReduce-programmet på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller on-demand HDInsight-kluster. [on-demand](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](transform-data.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.

Om du inte har gjort det tidigare i Azure Data Factory läser du [in introduktion till Azure Data Factory](introduction.md) och gör självstudien: [Självstudiekurs: omvandla data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln.

Se [Pig](transform-data-using-hadoop-pig.md) och [Hive](transform-data-using-hadoop-hive.md) för mer information om hur du kör Pig/Hive-skript på ett HDInsight-kluster från en pipeline med hjälp av HDInsight Pig- och Hive-aktiviteter.

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

## <a name="syntax-details"></a>Syntaxinformation

| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| namn              | Aktivitetens namn                     | Ja      |
| description       | Text som beskriver vad aktiviteten används för | Inga       |
| typ              | För MapReduce-aktivitet är aktivitetstypen HDinsightMapReduce | Ja      |
| linkedServiceName | Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) | Ja      |
| Classname         | Namn på den klass som ska utföras         | Ja      |
| jarLinkedService  | Referens till en Azure Storage Linked-tjänst som används för att lagra Jar-filerna. Om du inte anger den här länkade tjänsten används Azure Storage Linked Service som definierats i hdinsight-länkade tjänsten. | Inga       |
| jarFilePath       | Ange sökvägen till Jar-filer som lagras i Azure Storage som refereras av jarLinkedService. Filnamnet är skiftlägeskänsligt. | Ja      |
| jarlibs (burklibs)           | Strängmatris för sökvägen till Jar-biblioteksfilerna som refereras av jobbet som lagras i Azure Storage som definierats i jarLinkedService. Filnamnet är skiftlägeskänsligt. | Inga       |
| getDebugInfo      | Anger när loggfilerna kopieras till Azure Storage som används av HDInsight-klustret (eller) som anges av jarLinkedService. Tillåtna värden: Inga, Alltid eller Fel. Standardvärde: Ingen. | Inga       |
| Argument         | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som kommandoradsargument till varje aktivitet. | Inga       |
| Definierar           | Ange parametrar som nyckel-/värdepar för refererande i Hive-skriptet. | Inga       |



## <a name="example"></a>Exempel
Du kan använda HDInsight MapReduce-aktiviteten för att köra en MapReduce-jar-fil i ett HDInsight-kluster. I följande exempel på JSON-definition av en pipeline är HDInsight-aktiviteten konfigurerad för att köra en Mahout JAR-fil.

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
Du kan ange eventuella argument för MapReduce-programmet i **argumentavsnittet.** Vid körning visas några extra argument (till exempel mapreduce.job.tags) från MapReduce-ramverket. Om du vill skilja dina argument med argumenten MapReduce kan du använda både alternativ och värde som argument som visas i följande exempel (-s, --input, --output etc., är alternativ som omedelbart följs av deras värden).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [Hadoop Streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Körning av maskininlärningsbatch](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
