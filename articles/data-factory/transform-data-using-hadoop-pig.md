---
title: Transformera data med Hadoop Pig aktivitet i Azure Data Factory | Microsoft Docs
description: "Lär dig hur du kan använda Pig-aktivitet i ett Azure data factory för att köra Pig-skript på en på-begäran/din egen HDInsight-kluster."
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
ms.openlocfilehash: 7745f273283ff998b41fe59a10e705e7564a7bd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformera data med Hadoop Pig aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-pig-activity.md)
> * [Version 2 – förhandsversion](transform-data-using-hadoop-pig.md)

HDInsight Pig-aktiviteten i en Datafabrik [pipeline](concepts-pipelines-activities.md) Pig frågor körs på [egna](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-kluster. Den här artikeln bygger på den [data transformation aktiviteter](transform-data.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Pig aktivitet i V1](v1/data-factory-pig-activity.md).

Om du har använt Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](introduction.md) och gör den [Självstudier: Transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
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

| Egenskap            | Beskrivning                              | Krävs |
| ------------------- | ---------------------------------------- | -------- |
| namn                | Namnet på aktiviteten                     | Ja      |
| description         | Text som beskriver aktiviteten är det som används för | Nej       |
| typ                | För Hive-aktiviteten är aktivitetstypen HDinsightPig | Ja      |
| linkedServiceName   | Referens till HDInsight-klustret registreras som en länkad tjänst i Data Factory. Mer information om den här länkade tjänsten, se [Compute länkade tjänster](compute-linked-services.md) artikel. | Ja      |
| scriptLinkedService | Referens till en Azure Storage-länkade tjänst som används för att lagra Pig-skriptet som ska köras. Om du inte anger den här länkade tjänsten används Azure länkade lagringstjänsten definieras i länkad HDInsight-tjänst. | Nej       |
| ScriptPath          | Ange sökväg till skriptfilen lagras i Azure Storage som anges av scriptLinkedService. Filnamnet är skiftlägeskänslig. | Nej       |
| getDebugInfo        | Anger om filerna kopieras till Azure Storage används av HDInsight-kluster (eller) anges av scriptLinkedService. Tillåtna värden: None, alltid eller fel. Standardvärde: Ingen. | Nej       |
| Argument           | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som argument på kommandoraden för varje aktivitet. | Nej       |
| definierar             | Ange parametrar som nyckel/värde-par för refererar till i Pig-skriptet. | Nej       |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du Transformera data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution aktivitet](transform-data-using-machine-learning.md)
* [Aktiviteten lagrad procedur](transform-data-using-stored-procedure.md)
