---
title: Kör Hadoop MapReduce exempel på HDInsight - Azure | Microsoft Docs
description: Komma igång med MapReduce-exempel i jar-filer som ingår i HDInsight. Använda SSH för att ansluta till klustret och sedan använda Hadoop-kommando för att köra exemplet jobb.
keywords: hadoop exempel jar, hadoop exempel jar, hadoop mapreduce exempel, mapreduce-exempel
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1d2a0b9-1659-4fab-921e-4a8990cbb30a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 14f860d64c482ac7ef74512aea4850821d30132c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Kör MapReduce-exempel som ingår i HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Lär dig hur du kör MapReduce-exempel som ingår i Hadoop i HDInsight.

## <a name="prerequisites"></a>Förutsättningar

* **Ett HDInsight-kluster**: finns [komma igång med Hadoop med Hive i HDInsight på Linux](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]
    > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **En SSH-klient**: Mer information finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>MapReduce-exempel

**Plats**: exemplen finns på HDInsight-kluster på `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Innehållet**: I följande exempel finns i det här arkivet:

* `aggregatewordcount`: En aggregering baserat mapreduce-program som räknas orden i indatafilerna.
* `aggregatewordhist`: En aggregering baserat mapreduce-program som beräknar histogram ord i indatafilerna.
* `bbp`: Ett mapreduce-program som använder Bailey-Borwein-Plouffe för att beräkna exakta siffror pi.
* `dbcount`: Ett exempel jobb som räknar pageview loggfilerna lagras i en databas.
* `distbbp`: Ett mapreduce-program som använder en BBP-type-formel för att beräkna exakt bits pi.
* `grep`: Ett mapreduce-program som räknar matchningar av en regex i indata.
* `join`: Ett jobb som utför en koppling över sorteras, lika partitionerad datauppsättningar.
* `multifilewc`: Ett jobb som räknar ord från flera filer.
* `pentomino`: Ett mapreduce sida vid sida om program för att hitta lösningar på problem med pentomino.
* `pi`: Ett mapreduce-program som beräknar Pi med hjälp av en kvasi Monte Carlo-metoden.
* `randomtextwriter`: Ett mapreduce-program som skriver 10 GB av slumpmässiga textdata per nod.
* `randomwriter`: Ett mapreduce-program som skriver 10 GB slumpmässiga data per nod.
* `secondarysort`: Ett exempel som definierar en sekundär sortering till minska fasen.
* `sort`: Ett mapreduce-program som sorterar data som skrivs av slumpmässiga writer.
* `sudoku`: En sudoku solver.
* `teragen`: Skapa data för terasort.
* `terasort`: Kör terasort.
* `teravalidate`: Kontrollera resultatet av terasort.
* `wordcount`: Ett mapreduce-program som räknas orden i indatafilerna.
* `wordmean`: Ett mapreduce-program som räknar den genomsnittliga längden på texten i de inkommande filerna.
* `wordmedian`: Ett mapreduce-program som räknar ord i indatafilerna median längd.
* `wordstandarddeviation`: Ett mapreduce-program som räknar standardavvikelsen för längden på texten i de inkommande filerna.

**Källkoden**: källkoden för exemplen ingår i HDInsight-kluster på `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Köra wordcount-exemplet

1. Anslut till HDInsight med hjälp av SSH. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Från den `username@#######:~$` uppmanar, använder du följande kommando för att lista exemplen:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Det här kommandot skapar listan med exempel från föregående avsnitt i det här dokumentet.

3. Använd kommandot om du vill ha hjälp med ett specifikt exempel. I det här fallet den **wordcount** exempel:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Följande meddelande:

        Usage: wordcount <in> [<in>...] <out>

    Det här meddelandet innebär att du kan ange flera indatasökvägarna för källdokument. Den slutliga sökvägen är där utdata (antal ord i källdokument) lagras.

4. Använd följande för att räkna alla orden i den bärbara datorer för Leonardo Da Vinci, som tillhandahålls som exempeldata med ditt kluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Indata för det här jobbet har lästs från `/example/data/gutenberg/davinci.txt`. Utdata för det här exemplet är lagrad i `/example/data/davinciwordcount`. Både sökvägar finns på standardlagring för klustret, inte det lokala filsystemet.

   > [!NOTE]
   > Som anges i hjälpen för wordcount-exemplet, kan du också ange flera indatafiler. Till exempel `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` räknas orden i både davinci.txt och ulysses.txt.

5. När jobbet har slutförts använder du följande kommando du visar utdata:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Det här kommandot sammanfogar alla utdatafiler som produceras av jobbet. Den visar utdata till konsolen. De utdata som genereras liknar följande text:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Varje rad utgör ett ord och hur många gånger som det har uppstått i indata.

## <a name="the-sudoku-example"></a>Sudoku-exempel

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) är logik avgöra består av nio 3 x 3 rutnät. Vissa celler i rutnätet har tal, medan andra är tomt och målet är att lösa för tomma celler. Föregående länk har mer information om problem, men det här exemplet syftar till att lösa för tomma celler. Så ska våra indata vara en fil som finns i följande format:

* Nio rader nio kolumner
* Varje kolumn kan innehålla antingen ett tal eller `?` (som anger en tom cell)
* Celler avgränsat med ett blanksteg

Det finns ett visst sätt att konstruera Sudoku kanske; Du kan upprepa ett tal i en kolumn eller rad. Det finns ett exempel på HDInsight-klustret har skapats korrekt. Det finns i `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` och innehåller följande text:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Om du vill köra det här exemplet problemet genom Sudoku exempel, använder du följande kommando:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Resultatet liknar följande:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Exempel PI (π)

Pi används en statistisk (kvasi Monte Carlo) metod för att uppskatta värdet för pi. Punkter placeras slumpmässigt i en ruta för enheten. Kvadraten innehåller också en cirkel. Sannolikheten att pekar faller inom cirkeln är lika med området cirkel-pi/4. Värdet för pi beräknas från värdet för 4R. R är förhållandet mellan antalet punkter som finns inuti cirkeln till det totala antalet punkter som ligger inom kvadraten. Ju större det här exemplet används punkter bättre uppskattning är.

Använd följande kommando för att köra det här exemplet. Detta kommando använder 16 maps med 10 000 000 prover för att beräkna värdet för pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Värdet som returneras av kommandot liknar **3.14159155000000000000**. Referenser är de första 10 decimalerna pi 3.1415926535.

## <a name="10-gb-greysort-example"></a>10 GB Greysort exempel

GraySort är en benchmark-sortering. Måttet är sortera (TB per minut) som uppnås vid sortering av stora mängder data, vanligtvis en 100 TB minsta.

Det här exemplet använder en liten 10 GB data så att den kan köras relativt snabbt. MapReduce-program som utvecklats av Owen O'Malley och Arun Murthy används. Dessa program vann årliga allmänna (”daytona”) terabyte sortera prestandamått i 2009 med en andel 0.578 TB per minut (100 TB 173 minuter). Mer information om den här och andra sorterings prestandamått finns i [Sortbenchmark](http://sortbenchmark.org/) plats.

Det här exemplet använder tre olika MapReduce-program:

* **TeraGen**: A MapReduce-program som skapar rader för sortering

* **TeraSort**: exempel på indata och använder MapReduce för att sortera data i en total order

    TeraSort är en standard MapReduce-sortering, förutom en anpassad partitioneraren. Partitioneraren som använder en sorterad lista över N-1 provtagning nycklar som definierar nyckeln intervallet för varje minska. I synnerhet alla nycklar sådana som exempel [i-1] < = nyckel < exempel [i] skickas till minska i. Den här partitioneraren garanterar att utdata för minskar i är mindre än utdata från minska i + 1.

* **TeraValidate**: A MapReduce-program som verifierar att resultatet sorteras globalt

    Det skapar en mappning per fil i den angivna katalogen och varje mappning garanterar att varje nyckel är mindre än eller lika med det tidigare. Funktionen kartan genererar poster för de första och sista nycklarna för varje fil. Funktionen minska garanterar att den första nyckeln för filen i är större än den senaste nyckeln i filen i-1. Eventuella problem rapporteras som utdata för fasen minska med nycklar som är i fel ordning.

Använd följande steg för att generera data, sortera och sedan Validera utdata:

1. Generera en 10 GB data som lagras i HDInsight-klustret standardlagring på `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    Den `-Dmapred.map.tasks` talar om Hadoop hur många kartan uppgifter för det här jobbet. De två sista parametrarna instruera jobbet att skapa 10 GB data och spara den på `/example/data/10GB-sort-input`.

2. Använd följande kommando för att sortera data:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    Den `-Dmapred.reduce.tasks` talar om Hadoop hur många minska uppgifter som ska användas för jobbet. De sista två parametrarna är bara inkommande och utgående platserna för data.

3. Använd följande för att validera data som genereras av sorteringen:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Nästa steg

Från den här artikeln beskrivs hur du kör ingår i Linux-baserade HDInsight-kluster. Självstudier om hur du använder Pig, Hive och MapReduce med HDInsight finns i följande avsnitt:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

