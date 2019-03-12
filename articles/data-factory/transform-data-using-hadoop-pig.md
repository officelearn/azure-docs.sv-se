---
title: Transformera data med Hadoop Pig-aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda Pig-aktivitet i en Azure-datafabrik för att köra Pig-skript på ett på-begäran/your own HDInsight-kluster.
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
ms.openlocfilehash: 914bc37552a80886df16ed69fba4e31b3f22ac22
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574924"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformera data med Hadoop Pig-aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-pig-activity.md)
> * [Aktuell version](transform-data-using-hadoop-pig.md)

HDInsight-piggningsåtgärd i en Datafabrik [pipeline](concepts-pipelines-activities.md) kör Pig frågor på [egna](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-kluster. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](transform-data.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter.

Om du är nybörjare på Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](introduction.md) och gör den [självstudie: omvandla data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

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

| Egenskap             | Beskrivning                              | Krävs |
| ------------------- | ---------------------------------------- | -------- |
| namn                | Namn på aktiviteten                     | Ja      |
| beskrivning         | Text som beskriver vad aktiviteten används till | Nej       |
| typ                | Aktivitetstypen är HDinsightPig för Hive-aktivitet | Ja      |
| linkedServiceName   | Referens till HDInsight-kluster som är registrerad som en länkad tjänst i Datafabriken. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln. | Ja      |
| scriptLinkedService | Referens till en Azure Storage-länkade tjänst som används för att lagra Pig-skript som ska köras. Om du inte anger den här länkade tjänsten, används den Azure Storage länkade tjänsten definieras i den länkade tjänsten HDInsight. | Nej       |
| scriptPath          | Ange sökvägen till skriptfilen som lagras i Azure Storage som anges med scriptLinkedService. Filnamnet är skiftlägeskänsligt. | Nej       |
| getDebugInfo        | Anger om filerna kopieras till Azure Storage används av HDInsight-kluster (eller) anges med scriptLinkedService. Tillåtna värden: Ingen alltid kan eller inte. Standardvärde: Ingen. | Nej       |
| argument           | Anger en matris med argumenten för ett Hadoop-jobb. Argumenten skickas till varje aktivitet som kommandoradsargument. | Nej       |
| definierar             | Ange parametrar som nyckel/värde-par för den refererade Pig-skript. | Nej       |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som beskriver hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning-batchkörningsaktivitet](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
