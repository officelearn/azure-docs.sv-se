---
title: Transformera data med Hadoop Streaming activity i Azure Data Factory | Microsoft Docs
description: Beskriver hur du använder Hadoop Streaming Activity i Azure Data Factory för att omvandla data genom att köra Hadoop Streaming program på ett Hadoop-kluster.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 7c882e6fd826adb415b0452c9b441405d2ff90d7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformera data med Hadoop Streaming activity i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-hadoop-streaming-activity.md)
> * [Version 2 – förhandsversion](transform-data-using-hadoop-streaming.md)

HDInsight Streaming Activity i en Datafabrik [pipeline](concepts-pipelines-activities.md) Hadoop Streaming program körs på [egna](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-kluster. Den här artikeln bygger på den [data transformation aktiviteter](transform-data.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Hadoop Streaming Activity i V1](v1/data-factory-hadoop-streaming-activity.md).

Om du har använt Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](introduction.md) och gör den [Självstudier: Transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

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

| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| namn              | Namnet på aktiviteten                     | Ja      |
| description       | Text som beskriver aktiviteten är det som används för | Nej       |
| typ              | För Hadoop Streaming Activity är aktivitetstypen HDInsightStreaming | Ja      |
| linkedServiceName | Referens till HDInsight-klustret registreras som en länkad tjänst i Data Factory. Mer information om den här länkade tjänsten, se [Compute länkade tjänster](compute-linked-services.md) artikel. | Ja      |
| mapper            | Anger namnet på den körbara mapparen | Ja      |
| Reducer           | Anger namnet på den körbara reducer | Ja      |
| combiner          | Anger namnet på den körbara combiner | Nej       |
| fileLinkedService | Referens till en Azure Storage-länkade tjänst som används för att lagra Mapper och Combiner Reducer program som ska köras. Om du inte anger den här länkade tjänsten används Azure länkade lagringstjänsten definieras i länkad HDInsight-tjänst. | Nej       |
| filePath          | Ge en matris med sökvägen till Mapper, Combiner, och Reducer program lagras i Azure Storage som anges av fileLinkedService. Sökvägen är skiftlägeskänslig. | Ja      |
| Indata             | Anger WASB sökvägen till filen för mapparen. | Ja      |
| utdata            | Anger WASB sökväg till utdatafilen för Reducer. | Ja      |
| getDebugInfo      | Anger om filerna kopieras till Azure Storage används av HDInsight-kluster (eller) anges av scriptLinkedService. Tillåtna värden: None, alltid eller fel. Standardvärde: Ingen. | Nej       |
| Argument         | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som argument på kommandoraden för varje aktivitet. | Nej       |
| definierar           | Ange parametrar som nyckel/värde-par för refererar till i Hive-skript. | Nej       | 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du Transformera data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Pig-aktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution aktivitet](transform-data-using-machine-learning.md)
* [Aktiviteten lagrad procedur](transform-data-using-stored-procedure.md)
