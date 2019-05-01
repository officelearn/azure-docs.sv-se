---
title: Kör Apache Hadoop MapReduce-exempel på HDInsight - Azure
description: Kom igång med MapReduce-exemplen i jar-filer som ingår i HDInsight. Anslut till klustret via SSH och sedan använda kommandot Hadoop för att köra exemplet jobb.
keywords: exempel JAR-filen för hadoop, hadoop exempel jar, hadoop mapreduce-exempel, mapreduce-exempel
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: hrasheed
ms.openlocfilehash: f0251e3926c569b45ebebcd18b98df5af4564443
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706660"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Köra MapReduce-exemplen som ingår i HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Lär dig hur du kör MapReduce-exemplen som ingår i Apache Hadoop på HDInsight.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Apache Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>MapReduce-exempel

**Plats**: Exemplen finns på HDInsight-klustret på `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Innehållet**: Följande exempel finns i det här arkivet:

* `aggregatewordcount`: En aggregering baserat mapreduce-program som räknar orden i indatafilerna.
* `aggregatewordhist`: En aggregering baserat mapreduce-program som beräknar histogrammet av orden i indatafilerna.
* `bbp`: Ett mapreduce-program som använder Bailey-Borwein-Plouffe för att beräkna exakta siffrorna i Pi.
* `dbcount`: En exempel-jobb som räknar sidvisningar-loggar som lagras i en databas.
* `distbbp`: Ett mapreduce-program som använder en BBP-type-formel för att beräkna exakta bits pi.
* `grep`: Ett mapreduce-program som räknar matchningar av ett regex i indata.
* `join`: Ett jobb som utför en koppling sorterade, lika partitioneras datauppsättningar.
* `multifilewc`: Ett jobb som räknar ord från flera filer.
* `pentomino`: Ett mapreduce panel om program för att hitta lösningar på pentomino problem.
* `pi`: Ett mapreduce-program som beräknar Pi med hjälp av en kvasi Monte Carlo-metoden.
* `randomtextwriter`: Ett mapreduce-program som skriver 10 GB av slumpmässiga textdata per nod.
* `randomwriter`: Ett mapreduce-program som skriver 10 GB av slumpmässiga data per nod.
* `secondarysort`: Ett exempel som definierar en sekundär sortering för att minska fas.
* `sort`: Ett mapreduce-program som sorterar data som skrivits av slumpmässiga skrivaren.
* `sudoku`: En sudoku solver.
* `teragen`: Generera data för terasort.
* `terasort`: Kör terasort.
* `teravalidate`: Kontrollerar resultatet av terasort.
* `wordcount`: Ett mapreduce-program som räknar orden i indatafilerna.
* `wordmean`: Ett mapreduce-program som räknar av orden i indatafilerna genomsnittliga längd.
* `wordmedian`: Ett mapreduce-program som räknar median längden på orden i indatafilerna.
* `wordstandarddeviation`: Ett mapreduce-program som räknar standardavvikelsen för längden på orden i indatafilerna.

**Källkod**: Källkoden för dessa exempel ingår i HDInsight-klustret på `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Köra wordcount-exemplet

1. Ansluta till HDInsight med hjälp av SSH. Ersätt `CLUSTER` med namnet på klustret och ange sedan följande kommando:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Från den `username@#######:~$` uppmanar, använder du följande kommando för att lista exemplen:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Det här kommandot skapar listan med exemplet i föregående avsnitt i det här dokumentet.

3. Använd följande kommando om du vill ha hjälp med ett specifikt exempel. I det här fallet den **wordcount** exemplet:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Följande meddelande:

        Usage: wordcount <in> [<in>...] <out>

    Det här meddelandet innebär att du kan ange flera inkommande sökvägarna för källdokument. Den slutliga sökvägen är där utdata (antal ord i källdokument) lagras.

4. Använd följande för att räkna alla orden i den bärbara datorer för Leonardo da Vinci, som tillhandahålls som exempeldata med ditt kluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Indata för det här jobbet har lästs från `/example/data/gutenberg/davinci.txt`. Utdata för det här exemplet lagras i `/example/data/davinciwordcount`. Båda sökvägarna finns på standardlagringen för klustret, inte det lokala filsystemet.

   > [!NOTE]  
   > Enligt vad som anges i hjälpen för wordcount-exemplet, kan du också ange flera indatafiler. Till exempel `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` räknas orden i både davinci.txt och ulysses.txt.

5. När jobbet har slutförts, använder du följande kommando för att visa utdata:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Det här kommandot sammanfogar alla utdatafiler som skapas av jobbet. Den visar utdata till konsolen. De utdata som genereras liknar följande text:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Varje rad utgör ett ord och hur många gånger den inträffade i indata.

## <a name="the-sudoku-example"></a>Sudoku-exempel

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) är en logic pusselbiten består av nio 3 x 3 rutnät. Vissa celler i rutnätet har siffror, medan andra är tom och målet är att lösa för tomma celler. Den föregående länken innehåller mer information om pusselbiten, men syftet med det här exemplet är att hitta för tomma celler. Så att våra indata ska vara en fil som finns i följande format:

* Nio raderna i nio kolumner
* Varje kolumn kan innehålla antingen ett tal eller `?` (vilket betyder en tom cell)
* Celler är avgränsade med blanksteg

Det finns ett visst sätt att konstruera Sudoku pussel; Du kan inte Upprepa ett tal i en kolumn eller rad. Det finns ett exempel på HDInsight-kluster som skapas korrekt. Det finns i `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` och innehåller följande text:

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

Resultatet liknar följande text:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>PI (π)-exempel

Pi-exemplet används en statistisk (kvasi Monte Carlo) metoden för att beräkna värdet för pi. Punkter placeras slumpmässigt i en ruta för enheten. Kvadraten innehåller också en cirkel. Sannolikheten att pekar faller inom cirkeln är lika med området på cirkeln, pi/4. Värdet för pi kan beräknas från värdet för 4R. R är förhållandet mellan antalet punkter som är i cirkeln för det totala antalet punkter som ligger inom kvadraten. Ju större urval av punkter som används, desto bättre uppskattningen anges.

Använd följande kommando för att köra det här exemplet. Det här kommandot använder 16 maps med 10 000 000 exempel för att beräkna värdet för pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Värdet som returneras av det här kommandot liknar **3.14159155000000000000**. För referenser är de första 10 decimalerna pi 3.1415926535.

## <a name="10-gb-graysort-example"></a>10 GB GraySort-exemplet

GraySort är en benchmark-sortering. Måttet är sortera hastigheten (TB per minut) som uppnås vid sortering av stora mängder data, vanligtvis en 100 TB minsta.

Det här exemplet används en mycket små 10 GB data så att den kan köras relativt snabbt. MapReduce-program som har utvecklats av Owen O'Malley och Arun Murthy används. Dessa program vann årliga allmänna (”Daytona”) terabyte sortera prestandamått i 2009 med en hastighet av 0.578 TB per minut (100 TB på 173 minuter). Läs mer om den här och andra sortering prestandamått, den [sortera Benchmark](https://sortbenchmark.org/) plats.

Det här exemplet använder tre uppsättningar MapReduce-program:

* **TeraGen**: Ett MapReduce-program som genererar rader för sortering

* **TeraSort**: Exempel för indata och använder MapReduce för att sortera data i en totala ordning

    TeraSort är en standard MapReduce-sortering, förutom en anpassad partitioner. Partitioner använder en sorterad lista över N-1 samplas nycklar som definierar nyckelintervall för varje minska. I synnerhet, alla nycklar sådana som samplar [i-1] < = key < exemplet [i] skickas för att minska i. Den här partitioner garantier att utdata för minskar i är mindre än utdata från minska i + 1.

* **TeraValidate**: Ett MapReduce-program som kontrollerar att resultatet ska sorteras efter globalt

    En mappning per fil skapas i katalogen och varje diagram säkerställer att varje nyckel är mindre än eller lika med föregående. Funktionen kartan genererar poster för de första och sista nycklarna för varje fil. Funktionen minska säkerställer att den första nyckeln för filen i är större än den senaste nyckeln för filen i-1. Eventuella problem rapporteras som utdata för fasen minska med nycklar som är i fel ordning.

Använd följande steg för att generera data, sortera och sedan Validera utdata:

1. Generera 10 GB data som lagras till HDInsight-klustrets standard storage vid `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    Den `-Dmapred.map.tasks` talar om Hadoop hur många kartan uppgifter ska användas för det här jobbet. De sista två parametrarna instruera jobbet att skapa 10 GB data och lagra den på `/example/data/10GB-sort-input`.

2. Använd följande kommando för att sortera data:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    Den `-Dmapred.reduce.tasks` talar om Hadoop hur många minska uppgifter som ska användas för jobbet. De sista två parametrarna är bara de inkommande och utgående platserna för data.

3. Använd följande för att validera data som genereras av sorteringen:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Nästa steg

Från den här artikeln lärde du dig att köra de exempel som ingår med Linux-baserade HDInsight-kluster. Självstudier om hur du använder Pig, Hive och MapReduce med HDInsight finns i följande avsnitt:

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)