---
title: Använda Hadoop Pig med SSH på ett HDInsight-kluster - Azure | Microsoft Docs
description: Lär dig hur ansluter till ett Linux-baserade Hadoop-kluster med SSH, och sedan använda kommandot Pig köras interaktivt Pig Latin-instruktioner eller som ett batchjobb.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: c296e01096480b85aea52ace69f25aff39e3bd2d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Köra Pig-jobb på en Linux-baserade kluster med kommandot Pig (SSH)

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Lär dig hur ska köras interaktivt Pig-jobb från en SSH-anslutning till ditt HDInsight-kluster. Pig Latin programmeringsspråk kan du beskriva transformationer som tillämpas på indata till önskat resultat.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett Linux-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Ansluta med SSH

Använda SSH för att ansluta till ditt HDInsight-kluster. I följande exempel ansluter till ett kluster med namnet **myhdinsight** som-konto med namnet **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a id="pig"></a>Använd kommandot Pig

1. När du är ansluten, starta Pig-kommandoradsgränssnittet (CLI) med hjälp av följande kommando:

    ```bash
    pig
    ```

    Efter en stund Kommandotolken ändras till`grunt>`.

2. Ange följande sats:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    Det här kommandot läser in innehållet i filen sample.log i LOGGARNA. Du kan visa innehållet i filen med hjälp av följande sats:

    ```piglatin
    DUMP LOGS;
    ```

3. Därefter Transformera data genom att använda ett reguljärt uttryck för att extrahera endast loggningsnivån för varje post med hjälp av följande sats:

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Du kan använda **DUMP** att visa data efter omvandlingen. I det här fallet använder `DUMP LEVELS;`.

4. Fortsätta att tillämpa transformationer med hjälp av instruktionerna i följande tabell:

    | Pig Latin-instruktion | Instruktionen har |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Tar bort rader som innehåller ett null-värde för loggningsnivån och lagrar resultaten i `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Grupperar raderna genom att loggningsnivån och lagrar resultaten i `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Skapar en mängd som innehåller varje unik logg värde och hur många gånger den sker. De uppgifter som lagras i `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Sorterar loggningsnivåerna efter antal (fallande) och lagras i `RESULT`. |

    > [!TIP]
    > Använd `DUMP` att visa resultatet av transformeringen efter varje steg.

5. Du kan också spara resultaten av en omvandling med hjälp av den `STORE` instruktionen. Till exempel följande sats sparar den `RESULT` till den `/example/data/pigout` på standardlagring för klustret:

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]
   > Data lagras i katalogen som angetts i filer med namnet `part-nnnnn`. Om mappen redan finns felmeddelande ett.

6. Om du vill avsluta grunt prompten anger du följande sats:

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>Pig Latin batch-filer

Du kan också använda Pig-kommandot för att köra Pig Latin i en fil.

1. När du avslutar grunt uppmaningen använder du följande kommando för att skapa fil med namnet `pigbatch.pig`:

    ```bash
    nano ~/pigbatch.pig
    ```

2. Skriv eller klistra in följande rader:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

    När du är klar kan du använda __Ctrl__ + __X__, __Y__, och sedan __RETUR__ att spara filen.

3. Använd följande kommando för att köra den `pigbatch.pig` filen med hjälp av kommandot Pig.

    ```bash
    pig ~/pigbatch.pig
    ```

    När batch-jobbet har slutförts visas följande utdata:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Nästa steg

Allmän information om Pig i HDInsight finns i följande dokument:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

Mer information om andra sätt att arbeta med Hadoop i HDInsight finns i följande dokument:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)
