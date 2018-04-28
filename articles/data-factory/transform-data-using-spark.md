---
title: Transformera data med hjälp av Spark aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att omvandla data genom att köra Spark program från ett Azure data factory-pipelinen Spark-aktivitet.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 52b6489d14c016ce2efdd06614102a40651b94c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformera data med hjälp av Spark aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-spark.md)
> * [Version 2 – förhandsversion](transform-data-using-spark.md)

Spark-aktivitet i en Datafabrik [pipeline](concepts-pipelines-activities.md) kör ett Spark-program på [egna](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-kluster. Den här artikeln bygger på den [data transformation aktiviteter](transform-data.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter. När du använder en på-begäran Spark länkad tjänst Data Factory skapar automatiskt ett Spark-kluster för du just-in-time bearbeta data och tar bort klustret när bearbetningen är klar. 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Spark aktivitet i V1](v1/data-factory-spark.md).

> [!IMPORTANT]
> Spark aktiviteten stöder inte HDInsight Spark-kluster som använder ett Azure Data Lake Store som primär lagring.

## <a name="spark-activity-properties"></a>Spark Aktivitetsegenskaper
Här är exempel JSON-definitionen för en Spark-aktivitet:    

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
        "arguments": [ "arg1", "arg2" ],
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

I följande tabell beskrivs JSON-egenskaper som används i JSON-definitionen:

| Egenskap              | Beskrivning                              | Krävs |
| --------------------- | ---------------------------------------- | -------- |
| namn                  | Namnet på aktivitet i pipelinen.    | Ja      |
| description           | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ                  | Aktivitetstypen är HDInsightSpark för Spark-aktiviteten. | Ja      |
| linkedServiceName     | Namnet på den HDInsight Spark länkade tjänst som körs på Spark-program. Mer information om den här länkade tjänsten, se [Compute länkade tjänster](compute-linked-services.md) artikel. | Ja      |
| SparkJobLinkedService | Azure Storage länkade tjänst som äger Spark fil, beroenden och loggar.  Om du inte anger ett värde för den här egenskapen används lagring som är associerade med HDInsight-kluster. Värdet för den här egenskapen kan bara vara en länkad Azure Storage-tjänst. | Nej       |
| rootPath              | Azure Blob-behållaren och mappen som innehåller filen Spark. Filnamnet är skiftlägeskänslig. Referera till mappstrukturen avsnitt (nästa avsnitt) för ytterligare information om strukturen för den här mappen. | Ja      |
| entryFilePath         | Relativa sökvägen till rotmappen på Spark/kodpaketet. | Ja      |
| Klassnamn             | Programmets Java/Spark huvudsakliga klass      | Nej       |
| Argument             | En lista med kommandoradsargument till Spark-program. | Nej       |
| proxyUser             | Användarkontot som ska personifiera för att köra Spark-program | Nej       |
| sparkConfig           | Ange värden för egenskaper för Spark-konfiguration visas i avsnittet: [Spark-konfiguration – programegenskaper](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nej       |
| getDebugInfo          | Anger när Spark loggfilerna kopieras till Azure storage som används av HDInsight-kluster (eller) anges av sparkJobLinkedService. Tillåtna värden: None, alltid eller fel. Standardvärde: Ingen. | Nej       |

## <a name="folder-structure"></a>Mappstruktur
Spark jobb är mer utökningsbar än Pig/Hive-jobb. För Spark jobb, du kan ange flera beroenden som jar-paket (placeras i java KLASSÖKVÄGEN), python-filer (placerad i PYTHONPATH) och andra filer.

Skapa följande mappstruktur i Azure Blob-lagring som refereras av länkad HDInsight-tjänsten. Därefter kan du överföra beroende filer till lämpliga undermappar i rotmappen som representeras av **entryFilePath**. Till exempel överföra python till undermappen pyFiles och jar-filer till undermappen burkar i rotmappen. Vid körning kommer Data Factory-tjänsten följande mappstruktur i Azure Blob storage:     

| Sökväg                  | Beskrivning                              | Krävs | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (rot)            | Rotsökvägen för Spark-jobb i den länkade lagringstjänsten | Ja      | Mapp |
| &lt;Användardefinierade &gt; | Den sökväg som pekar på filen post för Spark-jobb | Ja      | Fil   |
| . / JAR: er                | Alla filer under den här mappen överförs och placeras på java-klassökvägen i klustret | Nej       | Mapp |
| . / pyFiles             | Alla filer under den här mappen överförs och placeras på PYTHONPATH i klustret | Nej       | Mapp |
| . / filer               | Alla filer under den här mappen överförs och placeras på utföraren arbetskatalogen | Nej       | Mapp |
| . / arkiveras            | Alla filer under den här mappen dekomprimeras | Nej       | Mapp |
| . / loggar                | Den mapp som innehåller loggarna från Spark-klustret. | Nej       | Mapp |

Här är ett exempel på en lagringsplats som innehåller två Spark jobbfiler i Azure Blob Storage som refereras av länkad HDInsight-tjänsten.

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
Se följande artiklar som förklarar hur du Transformera data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Pig-aktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution aktivitet](transform-data-using-machine-learning.md)
* [Aktiviteten lagrad procedur](transform-data-using-stored-procedure.md)
