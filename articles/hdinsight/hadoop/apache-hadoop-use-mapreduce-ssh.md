---
title: MapReduce och SSH-anslutning med Hadoop i HDInsight - Azure | Microsoft Docs
description: "Lär dig hur du använder SSH för att köra jobb för MapReduce med Hadoop i HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 1ce635486c00bb4d2e5589d4c35ef6dff593b069
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Använda MapReduce med Hadoop i HDInsight med SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Lär dig mer om att skicka MapReduce-jobb från en SSH (Secure Shell)-anslutning till HDInsight.

> [!NOTE]
> Om du redan är bekant med Linux-baserade Hadoop-servrar, men du är nybörjare på HDInsight, se [Linux-baserat HDInsight tips](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Förhandskrav

* Ett kluster på Linux-baserat HDInsight (Hadoop på HDInsight)

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En SSH-klient. Mer information finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Ansluta med SSH

Ansluta till klustret med SSH. Till exempel följande kommando ansluter till ett kluster med namnet **myhdinsight**:

```bash
ssh admin@myhdinsight-ssh.azurehdinsight.net
```

**Om du använder en nyckel för certifikat för SSH-autentisering**, du kan behöva ange platsen för den privata nyckeln i klientsystemet, till exempel:

```bash
ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net
```

**Om du använder ett lösenord för SSH-autentisering**, måste du ange lösenordet när du tillfrågas.

Mer information om hur du använder SSH med HDInsight finns [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Använda Hadoop-kommandon

1. När du är ansluten till HDInsight-klustret, använder du följande kommando ett MapReduce-jobb:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Detta kommando startar den `wordcount` -klassen, som ingår i den `hadoop-mapreduce-examples.jar` filen. Den använder den `/example/data/gutenberg/davinci.txt` dokumentet som indata och utdata lagras på `/example/data/WordCountOutput`.

    > [!NOTE]
    > Läs mer om MapReduce-jobb och exempeldata [använda MapReduce i Hadoop på HDInsight](hdinsight-use-mapreduce.md).

2. Jobbet sänder ut information när den bearbetar och returnerar information av följande slag när jobbet har slutförts:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. När jobbet har slutförts använder du följande kommando för att utdatafilerna:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Det här kommandot visar två filer, `_SUCCESS` och `part-r-00000`. Den `part-r-00000` filen innehåller utdata för jobbet.

    > [!NOTE]
    > Vissa MapReduce-jobb kan delas upp resultaten över flera **del-r-###** filer. I så fall använder den ### suffix för att ange ordningen för filerna.

4. Om du vill visa utdata, använder du följande kommando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Det här kommandot visar en lista över de ord som finns i den **wasb://example/data/gutenberg/davinci.txt** fil- och hur många gånger varje ord inträffade. Följande är ett exempel på de data som finns i filen:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Sammanfattning

Som du ser ger ett enkelt sätt att köra MapReduce-jobb i ett HDInsight-kluster och sedan visa jobbutdata Hadoop-kommandon.

## <a id="nextsteps"></a>Nästa steg

Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce på Hadoop och HDInsight](hdinsight-use-mapreduce.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
