---
title: MapReduce och SSH-anslutning med Apache Hadoop – Azure HDInsight
description: Lär dig hur du använder SSH för att köra MapReduce-jobb med Apache Hadoop på HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: b4075de1a184896d598c11d09ae2b2bda5e257ed
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044514"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Använda MapReduce med Apache Hadoop på HDInsight med SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Lär dig hur du skickar MapReduce-jobb från en SSH-anslutning (Secure Shell) till HDInsight.

> [!NOTE]
> Om du redan är bekant med att använda Linux-baserade Apache Hadoop-servrar, men du är nybörjare på HDInsight, se [Linux-baserade HDInsight-tips](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Förhandskrav

* Ett Linux-baserat HDInsight-kluster (Hadoop på HDInsight)

* En SSH-klient. Mer information finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Anslut med SSH

Anslut till klustret med SSH. Till exempel ansluter följande kommando till ett kluster med namnet **myhdinsight** som **sshuser** -kontot:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Om du använder en certifikat nyckel för SSH-autentisering**kan du behöva ange platsen för den privata nyckeln i klient systemet, till exempel:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Om du använder ett lösen ord för SSH-autentisering**måste du ange lösen ordet när du uppmanas till det.

Mer information om hur du använder SSH med HDInsight finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Använda Hadoop-kommandon

1. När du är ansluten till HDInsight-klustret använder du följande kommando för att starta ett MapReduce-jobb:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Det här kommandot startar `wordcount`-klassen, som finns i `hadoop-mapreduce-examples.jar`s filen. Det använder `/example/data/gutenberg/davinci.txt` dokumentet som indata och utdata lagras på `/example/data/WordCountOutput`.

    > [!NOTE]
    > Mer information om det här MapReduce-jobbet och exempel data finns i [använda MapReduce i Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md).

2. Jobbet avger information som bearbetas och returnerar information som liknar följande text när jobbet har slutförts:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. När jobbet har slutförts använder du följande kommando för att Visa utdatafilerna:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Det här kommandot visar två filer, `_SUCCESS` och `part-r-00000`. `part-r-00000`-filen innehåller utdata för det här jobbet.

    > [!NOTE]  
    > Vissa MapReduce-jobb kan dela resultaten i flera **del-r-#** # # #-filer. I så fall använder du # # # # suffix för att ange filernas ordning.

4. Om du vill visa utdata använder du följande kommando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Det här kommandot visar en lista över de ord som finns i **wasb://example/data/Gutenberg/DaVinci.txt** -filen och antalet gånger som varje ord har inträffat. Följande text är ett exempel på de data som finns i filen:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Sammanfattning

Som du kan se ger Hadoop-kommandon ett enkelt sätt att köra MapReduce-jobb i ett HDInsight-kluster och sedan Visa jobbets utdata.

## <a id="nextsteps"></a>Nästa steg

Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)

Information om andra sätt att arbeta med Hadoop i HDInsight:

* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
* [Använda Apache gris med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
