---
title: "Felsöka och analysera Hadoop-tjänster med heap - Dumpar i Azure | Microsoft Docs"
description: "Automatiskt samla in Dumpar heap för Hadoop-tjänster och placera i Azure Blob storage-konto för att felsöka och analys."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 6d1d4d47d279eb7a1f0bf1f587445683f0ace7a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Samla in heap Dumpar i Blob storage för att felsöka och analysera Hadoop-tjänster
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heap Dumpar innehåller en ögonblicksbild av programmets minne, inklusive värdena för variabler vid tiden för dumpen skapades. Så att de är användbara för att diagnostisera problem som uppstår vid körning. Heap minnesdumpar kan samlas in för Hadoop-tjänster och placeras inuti Azure Blob storage-konto för en användare under HDInsightHeapDumps automatiskt /.

Insamling av heap Dumpar för olika tjänster måste vara aktiverat för tjänster på enskilda kluster. Standardvärdet för den här funktionen ska vara inaktiverat för ett kluster. Dessa heap minnesdumpar kan vara stora, så är det lämpligt att övervaka Blob storage-konto där de sparas när samlingen har aktiverats.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Informationen i den här artikeln gäller bara för Windows-baserade HDInsight. Mer information om Linux-baserat HDInsight finns [aktivera heap Dumpar för Hadoop-tjänster på Linux-baserat HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Tjänster för heap minnesdumpar
Du kan aktivera heap Dumpar för följande tjänster:

* **hcatalog** -tempelton
* **hive** -hiveserver2 metastore, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resurshanteraren, nodemanager, timelineserver
* **hdfs** -datanode secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Konfigurationselement som möjliggör heap minnesdumpar
Om du vill aktivera heap Dumpar för en tjänst måste du ange lämpliga konfigurationselement i avsnittet för tjänsten, som anges av **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Värdet för **service_name** kan vara någon av tjänsterna som anges här: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resurshanteraren, nodemanager, timelineserver, datanode secondarynamenode, eller namenode.

## <a name="enable-using-azure-powershell"></a>Aktivera med hjälp av Azure PowerShell
Du kan exempelvis använda följande skript om du vill aktivera heap Dumpar med hjälp av Azure PowerShell för jobhistoryserver:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Aktivera med hjälp av .NET SDK
Om du vill aktivera heap Dumpar med hjälp av Azure HDInsight .NET SDK för jobhistoryserver kan du till exempel använda följande kod:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
