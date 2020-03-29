---
title: Omvandla data med hjälp av Hadoop Streaming-aktivitet
description: I artikeln beskrivs hur du ankÃ¤nder Hadoop Streaming Activity i Azure Data Factory fÃ¤nder data genom att kÃ¤ndeka Hadoop-program i ett Hadoop-kluster.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 1c12a10dfdf8e69cf05ab30d0e6aa48fea5803a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912899"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Omvandla data med hjälp av Hadoop Streaming-aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuell version](transform-data-using-hadoop-streaming.md)

HDInsight Streaming Activity i en Pipeline för [datafabrik](concepts-pipelines-activities.md) kör Hadoop Streaming-program på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller on-demand HDInsight-kluster. [on-demand](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](transform-data.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.

Om du inte har gjort det tidigare i Azure Data Factory läser du [in introduktion till Azure Data Factory](introduction.md) och gör [självstudien: omvandla data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

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

## <a name="syntax-details"></a>Syntaxinformation

| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| namn              | Aktivitetens namn                     | Ja      |
| description       | Text som beskriver vad aktiviteten används för | Inga       |
| typ              | För Hadoop Streaming Activity är aktivitetstypen HDInsightStreaming | Ja      |
| linkedServiceName | Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) | Ja      |
| Mapper            | Anger namnet på den körbara mapparen | Ja      |
| Reducering           | Anger namnet på den körbara reduceren | Ja      |
| Combiner          | Anger namnet på den körbara sammanslutningen | Inga       |
| fileLinkedService | Referens till en Azure Storage Linked-tjänst som används för att lagra mapper-, combiner- och reducer-program som ska köras. Om du inte anger den här länkade tjänsten används Azure Storage Linked Service som definierats i hdinsight-länkade tjänsten. | Inga       |
| Filepath          | Ange en matris med sökväg till programmen Mapper, Combiner och Reducer som lagras i Azure Storage som refereras av fileLinkedService. Sökvägen är skiftlägeskänslig. | Ja      |
| indata             | Anger WASB-sökvägen till indatafilen för Mapper. | Ja      |
| utdata            | Anger WASB-sökvägen till utdatafilen för Reducer. | Ja      |
| getDebugInfo      | Anger när loggfilerna kopieras till Azure Storage som används av HDInsight-klustret (eller) som anges av scriptLinkedService. Tillåtna värden: Inga, Alltid eller Fel. Standardvärde: Ingen. | Inga       |
| Argument         | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som kommandoradsargument till varje aktivitet. | Inga       |
| Definierar           | Ange parametrar som nyckel-/värdepar för refererande i Hive-skriptet. | Inga       | 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Körning av maskininlärningsbatch](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
