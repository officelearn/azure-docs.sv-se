---
title: Omvandla data med hadoop-grisaktivitet
description: Lär dig hur du kan använda Grisaktiviteten i en Azure-datafabrik för att köra Pig-skript på ett on-demand/ditt eget HDInsight-kluster.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 73ce8c670940a31af6a88f98bfd5880ede259e01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418906"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Omvandla data med hadoop-grisaktivitet i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-pig-activity.md)
> * [Aktuell version](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

HDInsight Pig-aktiviteten i en Pipeline för [datafabrik](concepts-pipelines-activities.md) kör Grisfrågor på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller on-demand HDInsight-kluster. [on-demand](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](transform-data.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.

Om du inte har gjort det tidigare i Azure Data Factory läser du [in introduktion till Azure Data Factory](introduction.md) och gör [självstudien: omvandla data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

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
## <a name="syntax-details"></a>Syntaxinformation

| Egenskap            | Beskrivning                              | Krävs |
| ------------------- | ---------------------------------------- | -------- |
| namn                | Aktivitetens namn                     | Ja      |
| description         | Text som beskriver vad aktiviteten används för | Inga       |
| typ                | För Hive-aktivitet är aktivitetstypen HDinsightPig | Ja      |
| linkedServiceName   | Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) | Ja      |
| scriptLinkedService | Referens till en Azure Storage Linked-tjänst som används för att lagra Pig-skriptet som ska köras. Om du inte anger den här länkade tjänsten används Azure Storage Linked Service som definierats i hdinsight-länkade tjänsten. | Inga       |
| scriptPath          | Ange sökvägen till skriptfilen som lagras i Azure Storage som refereras av scriptLinkedService. Filnamnet är skiftlägeskänsligt. | Inga       |
| getDebugInfo        | Anger när loggfilerna kopieras till Azure Storage som används av HDInsight-klustret (eller) som anges av scriptLinkedService. Tillåtna värden: Inga, Alltid eller Fel. Standardvärde: Ingen. | Inga       |
| Argument           | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som kommandoradsargument till varje aktivitet. | Inga       |
| Definierar             | Ange parametrar som nyckel-/värdepar för refererande i pigskriptet. | Inga       |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [MapReduce aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Körning av maskininlärningsbatch](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
