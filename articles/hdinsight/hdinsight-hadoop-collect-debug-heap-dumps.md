---
title: Felsöka och analysera Apache Hadoop-tjänster med heapdumpar - Azure
description: Automatiskt samla in heapdumpar för Apache Hadoop-tjänster och placera i Azure Blob storage-konto för felsökning och analys.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 2a399899c93addf966d3f2ec0e36d4b1c76b686f
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51038315"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-apache-hadoop-services"></a>Samla in heapdumpar i Blob storage för att felsöka och analysera Apache Hadoop-tjänster
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heapdumpar innehåller en ögonblicksbild av programmets minne, inklusive värdena för variabler vid tidpunkten dumpen skapades. Så att de är användbara för att diagnostisera problem som uppstår vid körning. Heapdumpar kan samlas in för Apache Hadoop-tjänster och placeras i Azure Blob storage-konto för en användare under HDInsightHeapDumps automatiskt /.

Insamling av heap dumps för olika tjänster måste vara aktiverat för tjänster på enskilda kluster. Standard för den här funktionen är att vara inaktiverat för ett kluster. Dessa heapdumpar kan vara stora, så det är lämpligt att övervaka Blob storage-konto där de sparas när samlingen har aktiverats.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informationen i den här artikeln gäller endast för Windows-baserade HDInsight. Information om Linux-baserat HDInsight finns i [aktivera heapdumpar för Hadoop-tjänster på Linux-baserat HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Berättigade tjänster för heapdumpar
Du kan aktivera heap dumps för följande tjänster:

* **Apache hcatalog** -tempelton
* **Apache hive** -hiveserver2 och metaarkiv, derbyserver
* **mapreduce** -jobhistoryserver
* **Apache yarn** -resourcemanager nodemanager, timelineserver
* **Apache hdfs** -datanode secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Konfigurationselement som aktiverar heap dumps
Om du vill aktivera heap dumps för en tjänst måste du ange lämplig konfigurationselement i avsnittet för tjänsten, som anges med **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Värdet för **service_name** kan vara någon av tjänsterna som anges här: tempelton, hiveserver2 och metaarkiv, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, eller namenode.

## <a name="enable-using-azure-powershell"></a>Aktivera med hjälp av Azure PowerShell
Du kan exempelvis använda följande skript om du vill aktivera heap dumps med hjälp av Azure PowerShell för jobhistoryserver:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Aktivera med hjälp av .NET SDK
Du kan exempelvis använda följande kod för att aktivera heap dumps med Azure HDInsight .NET SDK för jobhistoryserver:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
