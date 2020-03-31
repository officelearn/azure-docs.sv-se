---
title: Omvandla data med Hadoop Hive-aktivitet
description: Lär dig hur du kan använda Hive-aktiviteten i en Azure-datafabrik för att köra Hive-frågor på ett on-demand/ditt eget HDInsight-kluster.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/15/2019
ms.openlocfilehash: b4af3f897a12c71d73962735d3fe68d95f138cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912917"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Omvandla data med Hadoop Hive-aktivitet i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-hive-activity.md)
> * [Aktuell version](transform-data-using-hadoop-hive.md)

HDInsight Hive-aktiviteten i en Data [Factory-pipeline](concepts-pipelines-activities.md) kör Hive-frågor på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller on-demand HDInsight-kluster. [on-demand](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](transform-data.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.

Om du inte har gjort det tidigare i Azure Data Factory läser du [in introduktion till Azure Data Factory](introduction.md) och gör [självstudien: omvandla data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

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
## <a name="syntax-details"></a>Syntaxinformation
| Egenskap            | Beskrivning                                                  | Krävs |
| ------------------- | ------------------------------------------------------------ | -------- |
| namn                | Aktivitetens namn                                         | Ja      |
| description         | Text som beskriver vad aktiviteten används för                | Inga       |
| typ                | För Hive-aktivitet är aktivitetstypen HDinsightHive        | Ja      |
| linkedServiceName   | Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) | Ja      |
| scriptLinkedService | Referens till en Azure Storage Linked-tjänst som används för att lagra Hive-skriptet som ska köras. Om du inte anger den här länkade tjänsten används Azure Storage Linked Service som definierats i hdinsight-länkade tjänsten. | Inga       |
| scriptPath          | Ange sökvägen till skriptfilen som lagras i Azure Storage som refereras av scriptLinkedService. Filnamnet är skiftlägeskänsligt. | Ja      |
| getDebugInfo        | Anger när loggfilerna kopieras till Azure Storage som används av HDInsight-klustret (eller) som anges av scriptLinkedService. Tillåtna värden: Inga, Alltid eller Fel. Standardvärde: Ingen. | Inga       |
| Argument           | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som kommandoradsargument till varje aktivitet. | Inga       |
| Definierar             | Ange parametrar som nyckel-/värdepar för refererande i Hive-skriptet. | Inga       |
| frågaTimeout        | Frågetidsutgångsvärde (i minuter). Gäller när HDInsight-klustret är aktiverat med Enterprise Security Package aktiverat. | Inga       |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Körning av maskininlärningsbatch](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
