---
title: Omvandla data med hjälp av Spark-aktivitet
description: Lär dig hur du omvandlar data genom att köra Spark-program från en Azure-datafabrikspipeline med sparkaktiviteten.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/31/2018
ms.openlocfilehash: eb887a7d9081875c28964ddb1e3d1b2e609862fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912970"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Omvandla data med Spark-aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-spark.md)
> * [Aktuell version](transform-data-using-spark.md)

Spark-aktiviteten i en Data [Factory-pipeline](concepts-pipelines-activities.md) kör ett Spark-program på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller on-demand HDInsight-kluster. [on-demand](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](transform-data.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds. När du använder en spark-länkad tjänst på begäran skapar Data Factory automatiskt ett Spark-kluster där du precis är i tid för att bearbeta data och sedan tar bort klustret när bearbetningen är klar. 


## <a name="spark-activity-properties"></a>Egenskaper för sparkaktivitet
Här är exemplet JSON definition av en Spark Aktivitet:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

| Egenskap              | Beskrivning                              | Krävs |
| --------------------- | ---------------------------------------- | -------- |
| namn                  | Namnet på aktiviteten på pipelinen.    | Ja      |
| description           | Text som beskriver vad aktiviteten gör.  | Inga       |
| typ                  | För Spark-aktivitet är aktivitetstypen HDInsightSpark. | Ja      |
| linkedServiceName     | Namn på den HDInsight Spark Linked-tjänst som Spark-programmet körs på. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) | Ja      |
| SparkJobLinkedService | Azure Storage-länkade tjänsten som innehåller Spark-jobbfilen, beroenden och loggar.  Om du inte anger något värde för den här egenskapen används lagringen som är associerad med HDInsight-klustret. Värdet för den här egenskapen kan bara vara en Azure Storage-länkad tjänst. | Inga       |
| rootPath (rotPath)              | Azure Blob-behållaren och mappen som innehåller Spark-filen. Filnamnet är skiftlägeskänsligt. Mer information om mappens struktur finns i avsnittet mappstruktur (nästa avsnitt). | Ja      |
| entryFilePath         | Relativ sökväg till rotmappen för Spark-koden/-paketet. Postfilen måste vara antingen en Python-fil eller en JAR-fil. | Ja      |
| Classname             | Programmets huvudklass Java/Spark      | Inga       |
| Argument             | En lista med kommandoradsargument till Spark-programmet. | Inga       |
| proxyAnvändare             | Användarkontot som personifieras för att köra Spark-programmet | Inga       |
| sparkConfig (på andra sätt)           | Ange värden för spark-konfigurationsegenskaper som anges i avsnittet Spark [Configuration - Application properties](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Inga       |
| getDebugInfo          | Anger när Spark-loggfilerna kopieras till Azure-lagring som används av HDInsight-kluster (eller) som anges av sparkJobLinkedService. Tillåtna värden: Inga, Alltid eller Fel. Standardvärde: Ingen. | Inga       |

## <a name="folder-structure"></a>Mappstrukturen
Spark jobb är mer utökningsbara än Pig / Hive jobb. För Spark-jobb kan du ange flera beroenden, till exempel jar-paket (placerade i java CLASSPATH), pythonfiler (placerade på PYTHONPATH) och andra filer.

Skapa följande mappstruktur i Azure Blob-lagring som refereras av den HDInsight-länkade tjänsten. Ladda sedan upp beroende filer till lämpliga undermappar i rotmappen som representeras av **entryFilePath**. Ladda till exempel upp pythonfiler till undermappen pyFiles och jar-filerna till undermappen burkar i rotmappen. Vid körning förväntar sig Data Factory-tjänsten följande mappstruktur i Azure Blob-lagringen:     

| Sökväg                  | Beskrivning                              | Krävs | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`-Jag är inte så bra som jag vet.            | Rotsökvägen för Spark-jobbet i den lagringslänkade tjänsten | Ja      | Mapp |
| &lt;användardefinierad&gt; | Sökvägen som pekar på inmatningsfilen för Spark-jobbet | Ja      | Fil   |
| ./burkar                | Alla filer under den här mappen laddas upp och placeras på java classpath i klustret | Inga       | Mapp |
| ./pyFiles             | Alla filer under den här mappen laddas upp och placeras på PYTHONPATH i klustret | Inga       | Mapp |
| ./filer               | Alla filer under den här mappen laddas upp och placeras på executor arbetskatalog | Inga       | Mapp |
| ./arkiv            | Alla filer under den här mappen är okomprimerade | Inga       | Mapp |
| ./loggar                | Mappen som innehåller loggar från Spark-klustret. | Inga       | Mapp |

Här är ett exempel för en lagring som innehåller två Spark-jobbfiler i Azure Blob Storage som refereras av hdInsight-länkade tjänsten.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Körning av maskininlärningsbatch](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
