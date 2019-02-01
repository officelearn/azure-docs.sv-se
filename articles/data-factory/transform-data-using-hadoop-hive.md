---
title: Transformera data med Hadoop Hive-aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda Hive-aktivitet i Azure data factory för att köra Hive-frågor på ett på-begäran/your own HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: douglasl
ms.openlocfilehash: e77b8c89954064fb143f04f0f901d1512d4fc50d
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491016"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformera data med Hadoop Hive-aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-hive-activity.md)
> * [Aktuell version](transform-data-using-hadoop-hive.md)

HDInsight Hive-aktivitet i en Datafabrik [pipeline](concepts-pipelines-activities.md) kör Hive-frågor på [egna](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-kluster. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](transform-data.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter.

Om du är nybörjare på Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](introduction.md) och gör den [självstudie: omvandla data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| Egenskap             | Beskrivning                                                  | Krävs |
| ------------------- | ------------------------------------------------------------ | -------- |
| namn                | Namn på aktiviteten                                         | Ja      |
| beskrivning         | Text som beskriver vad aktiviteten används till                | Nej       |
| typ                | Aktivitetstypen är HDinsightHive för Hive-aktivitet        | Ja      |
| linkedServiceName   | Referens till HDInsight-kluster som är registrerad som en länkad tjänst i Datafabriken. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln. | Ja      |
| scriptLinkedService | Referens till en Azure Storage-länkade tjänst som används för att lagra Hive-skriptet som ska köras. Om du inte anger den här länkade tjänsten, används den Azure Storage länkade tjänsten definieras i den länkade tjänsten HDInsight. | Nej       |
| scriptPath          | Ange sökvägen till skriptfilen som lagras i Azure Storage som anges med scriptLinkedService. Filnamnet är skiftlägeskänsligt. | Ja      |
| getDebugInfo        | Anger om filerna kopieras till Azure Storage används av HDInsight-kluster (eller) anges med scriptLinkedService. Tillåtna värden: Ingen alltid kan eller inte. Standardvärde: Ingen. | Nej       |
| argument           | Anger en matris med argumenten för ett Hadoop-jobb. Argumenten skickas till varje aktivitet som kommandoradsargument. | Nej       |
| definierar             | Ange parametrar som nyckel/värde-par för refererar till Hive-skript. | Nej       |
| queryTimeout        | Fråga efter värdet för tidsgränsen (i minuter). Gäller när HDInsight-klustret är med Enterprise Security Package aktiverat. | Nej       |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som beskriver hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Piggningsåtgärd](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning-batchkörningsaktivitet](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
