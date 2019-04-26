---
title: Transformera data med Spark-aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du transformerar data genom att köra Spark program från en Azure data factory-pipeline med hjälp av Spark-aktivitet.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: cdf4dba3996668b3c9fe31df10050ff2cbff6cb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387833"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformera data med Spark-aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-spark.md)
> * [Aktuell version](transform-data-using-spark.md)

Spark-aktivitet i en Datafabrik [pipeline](concepts-pipelines-activities.md) kör ett Spark-program på [egna](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-kluster. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](transform-data.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter. När du använder en på begäran länkad Spark-tjänst kan Data Factory skapar automatiskt ett Spark-kluster för du just-in-time att bearbeta data och tar sedan bort klustret när bearbetningen är klar. 

> [!IMPORTANT]
> Spark-aktivitet har inte stöd för HDInsight Spark-kluster som använder en Azure Data Lake Store som primär lagring.

## <a name="spark-activity-properties"></a>Egenskaper för Spark-aktivitet
Här är exempel JSON-definition för en Spark-aktivitet:    

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
        "rootPath": "adfspark\\pyFiles",
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

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definition:

| Egenskap               | Beskrivning                              | Obligatoriskt |
| --------------------- | ---------------------------------------- | -------- |
| namn                  | Namnet på aktiviteten i pipelinen.    | Ja      |
| description           | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ                  | För Spark-aktivitet är aktivitetstypen HDInsightSpark. | Ja      |
| linkedServiceName     | Namnet på den HDInsight Spark länkade tjänst som Spark-programmet körs. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln. | Ja      |
| SparkJobLinkedService | Azure Storage-länkade tjänst som innehåller Spark jobbfilen, beroenden och loggar.  Om du inte anger ett värde för den här egenskapen används den lagring som är associerad med HDInsight-kluster. Värdet för den här egenskapen kan bara vara en länkad Azure Storage-tjänst. | Nej       |
| rootPath              | Azure Blob-behållaren och mappen som innehåller filen Spark. Filnamnet är skiftlägeskänsligt. Referera till mappstrukturen avsnitt (nästa avsnitt) för ytterligare information om strukturen för den här mappen. | Ja      |
| entryFilePath         | Relativa sökvägen till rotmappen för koden/paketet Spark. Post-filen måste vara antingen en Python-fil eller en .jar-fil. | Ja      |
| Klassnamn             | Programmets Java/Spark-huvudklass      | Nej       |
| argument             | En lista med kommandoradsargument till Spark-programmet. | Nej       |
| proxyUser             | Användarkonto för att personifiera för att köra Spark-programmet | Nej       |
| sparkConfig           | Ange värden för Spark-konfigurationsegenskaper som anges i avsnittet: [Spark-konfiguration – programegenskaper](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nej       |
| getDebugInfo          | Anger när Spark filerna kopieras till Azure storage används av HDInsight-kluster (eller) anges av sparkJobLinkedService. Tillåtna värden: Ingen alltid kan eller inte. Standardvärde: Ingen. | Nej       |

## <a name="folder-structure"></a>mappstruktur
Spark-jobb är mer omfattande än Pig/Hive-jobb. För Spark-jobb kan du ange flera beroenden som jar-paket (placeras i java KLASSÖKVÄGEN), python-filer (placeras på PYTHONPATH) och andra filer.

Skapa följande mappstrukturen i Azure Blob storage som refereras av den länkade HDInsight-tjänsten. Ladda sedan upp beroende filer till lämpliga undermappar i rotmappen som representeras av **entryFilePath**. Till exempel överföra python till undermappen pyFiles och jar-filer till undermappen JAR-filer i rotmappen. Vid körning förväntas Data Factory-tjänsten följande mappstrukturen i Azure Blob storage:     

| Sökväg                  | Beskrivning                              | Obligatoriskt | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (rot)            | Rotsökvägen för Spark-jobb i länkade storage-tjänsten | Ja      | Mapp |
| &lt;användardefinierad &gt; | Den sökväg som pekar startfil Spark-jobb | Ja      | Fil   |
| . / JAR: er                | Alla filer under den här mappen överförs och placeras på java-klassökvägen i klustret | Nej       | Mapp |
| ./pyFiles             | Alla filer under den här mappen överförs och placeras på PYTHONPATH i klustret | Nej       | Mapp |
| . / filer               | Alla filer under den här mappen överförs och placeras på executor arbetskatalog | Nej       | Mapp |
| . / arkiverar            | Alla filer under den här mappen är okomprimerade | Nej       | Mapp |
| . / loggar                | Den mapp som innehåller loggar från Spark-klustret. | Nej       | Mapp |

Här är ett exempel på en lagringsenhet som innehåller två Spark-jobbfiler i Azure Blob Storage som refereras av den länkade HDInsight-tjänsten.

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
Se följande artiklar som beskriver hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Piggningsåtgärd](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning-batchkörningsaktivitet](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
