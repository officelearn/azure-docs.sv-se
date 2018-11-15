---
title: MapReduce och SSH-anslutning med Apache Hadoop i HDInsight - Azure
description: Lär dig hur du använder SSH för att köra MapReduce-jobb med hjälp av Apache Hadoop på HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: df6a96d8bbf1c77d279806e69e7aa7f65387dd4b
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632534"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Använda MapReduce med Apache Hadoop i HDInsight med SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Lär dig hur du skickar MapReduce-jobb från en Secure Shell (SSH)-anslutning till HDInsight.

> [!NOTE]
> Om du redan är bekant med Linux-baserade Apache Hadoop-servrar, men du är nybörjare på HDInsight, se [tips för Linux-baserade HDInsight](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Förhandskrav

* Ett kluster för Linux-baserat HDInsight (Hadoop på HDInsight)

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En SSH-klient. Mer information finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Ansluta med SSH

Ansluta till klustret med SSH. Till exempel följande kommando ansluter till ett kluster med namnet **myhdinsight** som den **sshuser** konto:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Om du använder en nyckel för SSH-autentisering**, du kan behöva ange platsen för den privata nyckeln på ditt klientsystem, till exempel:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Om du använder ett lösenord för SSH-autentisering**, måste du ange lösenordet när du uppmanas till detta.

Mer information om hur du använder SSH med HDInsight finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Använda Hadoop-kommandon

1. När du är ansluten till HDInsight-kluster, använder du följande kommando till startar ett MapReduce-jobb:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Detta kommando startar den `wordcount` klass, som finns i den `hadoop-mapreduce-examples.jar` filen. Den använder den `/example/data/gutenberg/davinci.txt` dokumentet som indata och utdata lagras på `/example/data/WordCountOutput`.

    > [!NOTE]
    > Läs mer om MapReduce-jobb och exempeldata, [använda MapReduce i Hadoop på HDInsight](hdinsight-use-mapreduce.md).

2. Jobbet skickar information som bearbetas, och den returnerar information liknande följande text när jobbet har slutförts:

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
    > Vissa MapReduce-jobb kan delas upp resultaten i flera **del-r-###** filer. I så fall använda den ### suffix för att ange ordningen på filerna.

4. Om du vill visa utdata, använder du följande kommando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Det här kommandot visar en lista över de ord som finns i den **wasb://example/data/gutenberg/davinci.txt** fil- och hur många gånger varje ord inträffade. Följande text är ett exempel på de data som finns i filen:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Sammanfattning

Som du kan se ger ett enkelt sätt att köra MapReduce-jobb i ett HDInsight-kluster och sedan visa jobbutdata i Hadoop-kommandon.

## <a id="nextsteps"></a>Nästa steg

Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
