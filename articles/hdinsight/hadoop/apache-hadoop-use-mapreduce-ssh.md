---
title: MapReduce och SSH-anslutning med Apache Hadoop - Azure HDInsight
description: Lär dig hur du använder SSH för att köra MapReduce-jobb med Apache Hadoop på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934703"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Använd MapReduce med Apache Hadoop på HDInsight med SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Lär dig hur du skickar MapReduce-jobb från en SSH-anslutning (Secure Shell) till HDInsight.

> [!NOTE]
> Om du redan är bekant med att använda Linux-baserade Apache Hadoop-servrar, men du är ny på HDInsight, se [Linux-baserade HDInsight tips](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Krav

En Apache Hadoop kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Använda Hadoop-kommandon

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. När du är ansluten till HDInsight-klustret använder du följande kommando för att starta ett MapReduce-jobb:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Det här `wordcount` kommandot startar klassen, som `hadoop-mapreduce-examples.jar` finns i filen. Den använder `/example/data/gutenberg/davinci.txt` dokumentet som indata och `/example/data/WordCountOutput`utdata lagras vid .

    > [!NOTE]
    > Mer information om det här MapReduce-jobbet och exempeldata finns [i Använd MapReduce i Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md).

    Jobbet avger information när det bearbetas och returnerar information som liknar följande text när jobbet är slutfört:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. När jobbet är klart använder du följande kommando för att lista utdatafilerna:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Det här kommandot `_SUCCESS` visar `part-r-00000`två filer och . Filen `part-r-00000` innehåller utdata för det här jobbet.

    > [!NOTE]  
    > Vissa MapReduce-jobb kan dela upp resultaten mellan flera **del-r-########-filer.** Om så är fallet använder du suffixet ##### för att ange ordningen på filerna.

1. Om du vill visa utdata använder du följande kommando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Det här kommandot visar en lista över de ord som finns i **wasbs://example/data/gutenberg/davinci.txt** filen och antalet gånger varje ord har inträffat. Följande text är ett exempel på de data som finns i filen:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Nästa steg

Som du kan se ger Hadoop-kommandon ett enkelt sätt att köra MapReduce-jobb i ett HDInsight-kluster och sedan visa jobbutdata. Mer information om andra sätt kan du arbeta med Hadoop på HDInsight:

* [Använd MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Använd Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
