---
title: Använda Pig med Hadoop med SSH på ett HDInsight-kluster – Azure
description: Lär dig hur ansluter till ett Linux-baserade Hadoop-kluster med SSH och använder sedan Pig-kommando för att köra satser i Pig Latin interaktivt eller som ett batch-jobb.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 1deb44fecad6c120dd1e8f82cec36bff6353fc0b
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51003888"
---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Köra Pig-jobb i ett Linux-baserade kluster med kommandot Pig (SSH)

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Lär dig mer om att köra interaktivt Pig-jobb från en SSH-anslutning till ditt HDInsight-kluster. Pig Latin-programmeringsspråket kan du beskriva transformationer som tillämpas på indata till önskat resultat.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett Linux-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Ansluta med SSH

Använda SSH för att ansluta till ditt HDInsight-kluster. I följande exempel ansluter till ett kluster med namnet **myhdinsight** som-kontot med namnet **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a id="pig"></a>Använd kommandot Pig

1. När du är ansluten, startar du Pig-kommandoradsgränssnittet (CLI) med hjälp av följande kommando:

    ```bash
    pig
    ```

    Efter en stund Kommandotolken ändras till`grunt>`.

2. Ange följande uttryck:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    Det här kommandot läser in innehållet i sample.log-fil i LOGGARNA. Du kan visa innehållet i filen med hjälp av följande uttryck:

    ```piglatin
    DUMP LOGS;
    ```

3. Därefter Transformera data genom att använda ett reguljärt uttryck för att extrahera endast loggningsnivån från varje post med hjälp av följande uttryck:

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Du kan använda **DUMPA** att visa data efter omvandlingen. I det här fallet `DUMP LEVELS;`.

4. Fortsätta att tillämpa transformationer med hjälp av instruktionerna i följande tabell:

    | Pig Latin-instruktion | Instruktionen har |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Tar bort rader som innehåller ett nullvärde Loggnivå och lagrar resultatet i `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Grupperar rader av loggningsnivå och lagrar resultatet i `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Skapar en uppsättning som innehåller varje unik log nivån och hur många gånger den sker. De uppgifter som lagras i `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Beställningar loggningsnivåer av antal (fallande) och lagrar i `RESULT`. |

    > [!TIP]
    > Använd `DUMP` att visa resultatet av transformeringen efter varje steg.

5. Du kan också spara resultatet av en omvandling med hjälp av den `STORE` instruktionen. Till exempel följande instruktion sparar den `RESULT` till den `/example/data/pigout` på standardlagringen för klustret:

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]
   > Data lagras i den angivna katalogen i filer med namnet `part-nnnnn`. Om katalogen redan finns felmeddelande ett.

6. Om du vill avsluta grunt-prompten anger du följande uttryck:

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>Pig Latin batch-filer

Du kan också använda Pig-kommando för att köra Pig Latin i en fil.

1. När du avslutar grunt-prompten, använder du följande kommando för att skapa fil med namnet `pigbatch.pig`:

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
