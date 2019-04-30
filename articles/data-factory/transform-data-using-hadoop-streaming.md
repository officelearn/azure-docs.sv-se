---
title: Transformera data med Hadoop Streaming activity i Azure Data Factory | Microsoft Docs
description: Beskriver hur du använder Hadoop Streaming Activity i Azure Data Factory för att omvandla data genom att köra Hadoop Streaming program på ett Hadoop-kluster.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 0d8267f1cd65f78d5e98ae9d288d5fa5c4214420
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848256"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformera data med Hadoop Streaming activity i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuell version](transform-data-using-hadoop-streaming.md)

HDInsight-Strömningsaktivitet i en Datafabrik [pipeline](concepts-pipelines-activities.md) Hadoop Streaming program körs på [egna](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-kluster. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](transform-data.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter.

Om du är nybörjare på Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](introduction.md) och gör den [självstudie: omvandla data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

## <a name="json-sample"></a>JSON-exempel
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
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
| description       | Text som beskriver vad aktiviteten används till | Nej       |
| typ              | Aktivitetstyp av är Hadoop Streaming Activity HDInsightStreaming | Ja      |
| linkedServiceName | Referens till HDInsight-kluster som är registrerad som en länkad tjänst i Datafabriken. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln. | Ja      |
| händelsemappning            | Anger namnet på den körbara mapparen | Ja      |
| Reducer           | Anger namnet på den körbara reducer | Ja      |
| combiner          | Anger namnet på den körbara combiner | Nej       |
| fileLinkedService | Referens till en Azure Storage-länkade tjänst som används för att lagra mappning och Combiner Reducer program som ska köras. Om du inte anger den här länkade tjänsten, används den Azure Storage länkade tjänsten definieras i den länkade tjänsten HDInsight. | Nej       |
| filePath          | Tillhandahålla en matris med sökvägen till mappningen Combiner, och Reducer program som lagras i Azure Storage som refereras till av fileLinkedService. Sökvägen är skiftlägeskänslig. | Ja      |
| indata             | Anger WASB-sökväg till indatafilen för mappningen. | Ja      |
| utdata            | Anger WASB-sökväg till utdatafilen för Reducer. | Ja      |
| getDebugInfo      | Anger om filerna kopieras till Azure Storage används av HDInsight-kluster (eller) anges med scriptLinkedService. Tillåtna värden: Ingen alltid kan eller inte. Standardvärde: Ingen. | Nej       |
| argument         | Anger en matris med argumenten för ett Hadoop-jobb. Argumenten skickas till varje aktivitet som kommandoradsargument. | Nej       |
| definierar           | Ange parametrar som nyckel/värde-par för refererar till Hive-skript. | Nej       | 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som beskriver hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Piggningsåtgärd](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning-batchkörningsaktivitet](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
