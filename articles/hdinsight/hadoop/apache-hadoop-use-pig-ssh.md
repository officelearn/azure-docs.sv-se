---
title: "Använda Hadoop Pig med SSH på ett HDInsight-kluster - Azure | Microsoft Docs"
description: "Lär dig hur ansluter till ett Linux-baserade Hadoop-kluster med SSH, och sedan använda kommandot Pig köras interaktivt Pig Latin-instruktioner eller som ett batchjobb."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.openlocfilehash: be18f6db46285233e233c843dab1f389cd553e96
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Köra Pig-jobb på en Linux-baserade kluster med kommandot Pig (SSH)

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Lär dig hur ska köras interaktivt Pig-jobb från en SSH-anslutning till ditt HDInsight-kluster. Pig Latin programmeringsspråk kan du beskriva transformationer som tillämpas på indata till önskat resultat.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett Linux-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Ansluta med SSH

Använda SSH för att ansluta till ditt HDInsight-kluster. I följande exempel ansluter till ett kluster med namnet **myhdinsight** som-konto med namnet **sshuser**:

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net

**Om du har angett en certifikat-nyckel för SSH-autentisering** när du har skapat HDInsight-klustret kan du behöva ange platsen för den privata nyckeln på datorn för klienten.

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Om du har angett ett lösenord för SSH-autentisering** när du skapade HDInsight-kluster, ange lösenordet när du tillfrågas.

Mer information om hur du använder SSH med HDInsight finns [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Använd kommandot Pig

1. När du är ansluten, starta Pig-kommandoradsgränssnittet (CLI) med hjälp av följande kommando:

        pig

    Efter en stund bör du se en `grunt>` prompt.

2. Ange följande sats:

        LOGS = LOAD '/example/data/sample.log';

    Det här kommandot läser in innehållet i filen sample.log i LOGGARNA. Du kan visa innehållet i filen med hjälp av följande sats:

        DUMP LOGS;

3. Därefter Transformera data genom att använda ett reguljärt uttryck för att extrahera endast loggningsnivån för varje post med hjälp av följande sats:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

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

        STORE RESULT into '/example/data/pigout';

   > [!NOTE]
   > Data lagras i katalogen som angetts i filer med namnet `part-nnnnn`. Om mappen redan finns felmeddelande ett.

6. Om du vill avsluta grunt prompten anger du följande sats:

        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin batch-filer

Du kan också använda Pig-kommandot för att köra Pig Latin i en fil.

1. När du avslutar grunt uppmaningen, använder du följande kommando till pipe STDIN till en fil med namnet `pigbatch.pig`. Den här filen skapas i arbetskatalogen för SSH-användarkontot.

        cat > ~/pigbatch.pig

2. Skriv eller klistra in följande rader och sedan använda Ctrl + D när du är klar.

        LOGS = LOAD '/example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

3. Använd följande kommando för att köra den `pigbatch.pig` filen med hjälp av kommandot Pig.

        pig ~/pigbatch.pig

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
