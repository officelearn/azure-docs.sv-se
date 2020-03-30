---
title: Kör Apache Hadoop MapReduce exempel på HDInsight - Azure
description: Kom igång med MapReduce-exempel i jar-filer som ingår i HDInsight. Använd SSH för att ansluta till klustret och använd sedan kommandot Hadoop för att köra exempeljobb.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 58f7d99af638c8d03bbce46b7fcf8204aaca11d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435755"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Kör MapReduce-exemplen som ingår i HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Lär dig hur du kör MapReduce-exemplen som ingår i Apache Hadoop på HDInsight.

## <a name="prerequisites"></a>Krav

* En Apache Hadoop kluster på HDInsight. Se [Komma igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>MapReduce-exemplen

Exemplen finns i HDInsight-klustret på `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`. Källkod för dessa exempel ingår i HDInsight-klustret på `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

Följande exempel finns i det här arkivet:

|Exempel |Beskrivning |
|---|---|
|mängdordräkning|Räknar orden i indatafilerna.|
|aggregist|Beräknar ordindatans histogram i indatafilerna.|
|Bbp|Använder Bailey-Borwein-Plouffe för att beräkna exakta siffror för Pi.|
|dbcount (olika)|Räknar sidvisningsloggarna som lagras i en databas.|
|distbbp (olikart)|Använder en FORMEL AV BBP-typ för att beräkna exakta bitar av Pi.|
|Grep|Räknar matchningarna för en regex i indata.|
|join|Utför en koppling över sorterade, lika partitionerade datauppsättningar.|
|multifilewc|Räknar ord från flera filer.|
|pentomino (påt)|Kakel om program för att hitta lösningar på pentomino problem.|
|Pi|Uppskattar Pi med en kvasi-Monte Carlo-metod.|
|randomtextwriter|Skriver 10 GB slumpmässiga textdata per nod.|
|randomwriter (randomwriter)|Skriver 10 GB slumpmässiga data per nod.|
|sekundärort|Definierar en sekundär sortering till reducerfasen.|
|sortera|Sorterar data skrivna av den slumpmässiga författaren.|
|Sudoku|En sudoku lösare.|
|teragen (teragen)|Generera data för terasort.|
|terasort (olika)|Kör terasort.|
|teravalidat|Kontrollera resultaten av terasort.|
|wordcount (ort)|Räknar orden i indatafilerna.|
|wordmean (ordorde)|Räknar ordlängden i indatafilerna.|
|wordmedian (ort)|Räknar ordens medianlängd i indatafilerna.|
|wordstandarddeviation|Räknar standardavvikelsen för ordens längd i indatafilerna.|

## <a name="run-the-wordcount-example"></a>Kör wordcount-exemplet

1. Anslut till HDInsight med SSH. Ersätt `CLUSTER` med namnet på klustret och ange sedan följande kommando:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Från SSH-sessionen använder du följande kommando för att lista exemplen:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Det här kommandot genererar en lista med exempel från föregående avsnitt i det här dokumentet.

3. Använd följande kommando för att få hjälp med ett visst exempel. I det här fallet **wordcount-exemplet:**

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Du får följande meddelande:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Det här meddelandet anger att du kan ange flera inmatningssökvägar för källdokumenten. Den slutliga sökvägen är där utdata (antal ord i källdokumenten) lagras.

4. Använd följande om du vill räkna alla ord i Leonardo da Vincis anteckningsböcker, som tillhandahålls som exempeldata med klustret:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Indata för det `/example/data/gutenberg/davinci.txt`här jobbet läss från . Utdata för det `/example/data/davinciwordcount`här exemplet lagras i . Båda sökvägarna finns på standardlagring för klustret, inte det lokala filsystemet.

   > [!NOTE]  
   > Som anges i hjälpen för wordcount-exemplet kan du också ange flera indatafiler. Till exempel `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` skulle räkna ord i både davinci.txt och ulysses.txt.

5. När jobbet är klart använder du följande kommando för att visa utdata:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Det här kommandot sammanfogar alla utdatafiler som produceras av jobbet. Den visar utdata till konsolen. De utdata som genereras liknar följande text:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Varje rad representerar ett ord och hur många gånger det inträffade i indata.

## <a name="the-sudoku-example"></a>Exemplet Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) är en logik pussel som består av nio 3x3 rutnät. Vissa celler i rutnätet har siffror, medan andra är tomma, och målet är att lösa för de tomma cellerna. Den tidigare länken har mer information om pusslet, men syftet med detta prov är att lösa för de tomma cellerna. Så vår input bör vara en fil som är i följande format:

* Nio rader med nio kolumner
* Varje kolumn kan innehålla `?` antingen ett tal eller (som anger en tom cell)
* Cellerna avgränsas med ett blanksteg

Det finns ett visst sätt att konstruera Sudoku pussel; Du kan inte upprepa ett tal i en kolumn eller rad. Det finns ett exempel på HDInsight-klustret som är korrekt konstruerat. Den finns `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` på och innehåller följande text:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Om du vill köra det här exempelproblemet via Exemplet Sudoku använder du följande kommando:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Resultaten liknar följande text:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Pi (π) exempel

Pi-provet använder en statistisk (kvasi-Monte Carlo) metod för att uppskatta värdet av pi. Punkter placeras slumpmässigt i en enhetsruta. Torget innehåller också en cirkel. Sannolikheten att punkterna faller inom cirkeln är lika med området för cirkeln, pi/4. Värdet på pi kan beräknas från värdet av 4R. R är förhållandet mellan antalet punkter som finns inuti cirkeln och det totala antalet punkter som finns inom kvadraten. Ju större urval av punkter som används, desto bättre uppskattning är.

Använd följande kommando för att köra det här exemplet. Det här kommandot använder 16 kartor med 10 000 000 prover vardera för att uppskatta värdet på pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Värdet som returneras av det här kommandot liknar **3.141591550000000000000.** För referenser är de första 10 decimalerna pi 3,1415926535.

## <a name="10-gb-graysort-example"></a>10 GB GraySort exempel

GraySort är en benchmark-sortering. Måttet är sorteringshastigheten (TB/minut) som uppnås när stora mängder data sorteras, vanligtvis ett minimum på 100 TB.

Det här exemplet använder blygsamma 10 GB data så att den kan köras relativt snabbt. Den använder MapReduce applikationer som utvecklats av Owen O'Malley och Arun Murthy. Dessa ansökningar vann det årliga allmänna riktmärket (Daytona) terabyte sortera under 2009, med en hastighet av 0,578 TB / min (100 TB i 173 minuter). Mer information om detta och andra sorteringsriktmärken finns på webbplatsen [Sortera benchmark.](https://sortbenchmark.org/)

I det här exemplet används tre uppsättningar MapReduce-program:

* **TeraGen**: Ett MapReduce-program som genererar rader med data för att sortera

* **TeraSort**: Tar prover på indata och använder MapReduce för att sortera data i en total ordning

    TeraSort är en standard MapReduce-sortering, med undantag för en anpassad partitioner. Partitioner använder en sorterad lista med N-1-exempelnycklar som definierar nyckelintervallet för varje minska. I synnerhet skickas alla nycklar så att provet[i-1] <= nyckel < prov[i] för att minska i. Denna partitioner garanterar att utgångarna av minska jag är alla mindre än utdata för att minska i + 1.

* **TeraValidate**: Ett MapReduce-program som verifierar att utdata är globalt sorterade

    Den skapar en karta per fil i utdatakatalogen, och varje karta säkerställer att varje nyckel är mindre än eller lika med den föregående. Kartfunktionen genererar poster för de första och sista nycklarna i varje fil. Funktionen Minska säkerställer att den första nyckeln till filen i är större än den sista nyckeln till filen i-1. Eventuella problem rapporteras som en utdata från reduce-fasen, med nycklar som är ur funktion.

Använd följande steg för att generera data, sortera och sedan validera utdata:

1. Generera 10 GB data, som lagras i HDInsight-klustrets standardlagring vid: `/example/data/10GB-sort-input`

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    Talar `-Dmapred.map.tasks` om för Hadoop hur många kartuppgifter som ska användas för det här jobbet. De två sista parametrarna instruerar jobbet att skapa 10 GB data och att lagra den på `/example/data/10GB-sort-input`.

2. Använd följande kommando för att sortera data:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    Berättar `-Dmapred.reduce.tasks` Hadoop hur många minska uppgifter att använda för jobbet. De två sista parametrarna är bara in- och utdataplatser för data.

3. Använd följande för att validera data som genereras av sorten:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Nästa steg

Från den här artikeln har du lärt dig hur du kör de exempel som ingår i Linux-baserade HDInsight-kluster. Självstudier om hur du använder Pig, Hive och MapReduce med HDInsight finns i följande avsnitt:

* [Använd Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
* [Använd MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)