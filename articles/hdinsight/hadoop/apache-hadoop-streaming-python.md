---
title: Utveckla Python strömning MapReduce-jobb med HDInsight - Azure | Microsoft Docs
description: Lär dig hur du använder Python i strömning MapReduce-jobb. Hadoop innehåller ett strömmande API för MapReduce för att skriva på andra språk än Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: b5e19f81c3e869347f21ab3c70a70016196b946d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400522"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Utveckla Python strömning MapReduce program för HDInsight

Lär dig hur du använder Python i strömning MapReduce åtgärder. Hadoop innehåller ett strömmande API för MapReduce där du kan skriva kartan och minska funktioner på andra språk än Java. Stegen i det här dokumentet implementera kartan och minska komponenter i Python.

## <a name="prerequisites"></a>Förutsättningar

* En Linux-baserade Hadoop på HDInsight-kluster

  > [!IMPORTANT]
  > Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En textredigerare

  > [!IMPORTANT]
  > Textredigeraren måste använda LF rad avslutas. Med hjälp av en rad avslutades av CRLF orsakar fel när MapReduce-jobbet körs på Linux-baserade HDInsight-kluster.

* Den `ssh` och `scp` kommandon, eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Räkna ord

Det här exemplet är en grundläggande ordräkning genomföras i en python en mapper och reducer. Mapparen bryter meningar i individuella ord och reducer aggregerar orden och räknar om du vill generera utdata.

I följande flödesschema visar vad som händer under kartan och minska faser.

![Bild av mapreduce-processen](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Strömmande MapReduce

Hadoop kan du ange en fil som innehåller kartans och minska logik som används av ett jobb. Särskilda krav för kartan och minska logik är:

* **Inkommande**: kartan och minska komponenter måste läsa indata från STDIN.
* **Utdata**: kartan och minska komponenter måste skriva utdata till STDOUT.
* **Dataformatet**: data används och producerade måste vara ett nyckel/värde-par, avgränsade med semikolon fliken.

Python kan enkelt hantera dessa krav med hjälp av den `sys` modulen att läsa från STDIN och använder `print` skriva ut till STDOUT. Den sista aktiviteten bara formaterar data med en flik (`\t`) tecken mellan nyckel och värde.

## <a name="create-the-mapper-and-reducer"></a>Skapa mapper och reducer

1. Skapa en fil med namnet `mapper.py` och använda följande kod som innehållet:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Skapa en fil med namnet **reducer.py** och använda följande kod som innehållet:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Kör med PowerShell

Använd följande PowerShell-skript för att säkerställa att dina filer har rätt radbrytningar:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Använd följande PowerShell-skript för att överföra filer, kör jobbet och visar utdata:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Kör från en SSH-session

1. Från din utvecklingsmiljö i samma katalog som `mapper.py` och `reducer.py` filer, använder du följande kommando:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Ersätt `username` med SSH-användarnamn för klustret, och `clustername` med namnet på klustret.

    Det här kommandot kopieras filerna från det lokala systemet till huvudnod.

    > [!NOTE]
    > Om du använde ett lösenord för att skydda ditt konto med SSH, uppmanas för lösenordet. Om du använder en SSH-nyckel måste du kanske använda den `-i` parametern och sökvägen till den privata nyckeln. Till exempel `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Anslut till klustret med hjälp av SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Mer information om finns [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. För att säkerställa mapper.py och reducer.py har rätt radbrytningar, använder du följande kommandon:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Använd följande kommando för att starta MapReduce-jobbet.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Det här kommandot har följande delar:

   * **hadoop-streaming.jar**: används när du utför strömmande MapReduce-åtgärder. Det gränssnitt Hadoop med den externa MapReduce-kod som du anger.

   * **-filer**: lägger till de angivna filerna till MapReduce-jobb.

   * **-mapper**: Anger vilken fil som ska användas som mapparen för Hadoop.

   * **-reducer**: Anger vilken fil som ska användas som reducer för Hadoop.

   * **-inkommande**: indatafilen som vi ska räkna ord från.

   * **-utdata**: katalogen som utdata skrivs till.

    Eftersom MapReduce-jobb fungerar, visas processen som procenttal.

        05-02-15 19:01:04 INFO mapreduce. Jobbet: karta 0% minska 0% 05-02-15 19:01:16 INFO mapreduce. Jobbet: karta 100% minska 0% 05-02-15 19:01:27 INFO mapreduce. Jobbet: karta 100% minska 100%


5. Om du vill visa utdata, använder du följande kommando:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Det här kommandot visar en lista över ord och hur många gånger ordet inträffade.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder strömmande MapRedcue jobb med HDInsight, Använd följande länkar för att undersöka andra sätt att arbeta med Azure HDInsight.

* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)
