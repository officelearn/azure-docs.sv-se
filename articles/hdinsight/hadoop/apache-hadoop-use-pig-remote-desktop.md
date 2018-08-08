---
title: Använda Pig med Hadoop med fjärrskrivbord i HDInsight - Azure
description: Lär dig hur du använder Pig-kommandot för att köra satser i Pig Latin från en fjärrskrivbordsanslutning till en Windows-baserade Hadoop-kluster i HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 5aec07a5ebbbb56abcbaebbddc5579cf4d076b4d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590674"
---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Köra Pig-jobb från en fjärrskrivbordsanslutning
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Det här dokumentet innehåller en genomgång där Pig-kommando för att köra satser i Pig Latin från en fjärrskrivbordsanslutning till en Windows-baserade HDInsight-kluster. Pig Latin kan du skapa MapReduce-program genom att beskriva Datatransformationer, snarare än mappa och minska funktioner.

> [!IMPORTANT]
> Fjärrskrivbord är bara tillgängligt på HDInsight-kluster som använder Windows som operativsystem. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> För HDInsight 3.4 eller större finns i [använda Pig med HDInsight och SSH](apache-hadoop-use-pig-ssh.md) information om hur du kör interaktivt Pig-jobb direkt i klustret från en kommandorad.

## <a id="prereq"></a>Förhandskrav
Du behöver följande för att slutföra stegen i den här artikeln.

* Ett kluster för Windows-baserade HDInsight (Hadoop på HDInsight)
* En klientdator som kör Windows 10, Windows 8 eller Windows 7

## <a id="connect"></a>Ansluta med fjärrskrivbord
Aktivera Fjärrskrivbord för HDInsight-klustret och sedan ansluta till den genom att följa anvisningarna i [Anslut till HDInsight-kluster med RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>Använd kommandot Pig
1. När du har en anslutning till fjärrskrivbord kan du starta den **Hadoop-kommandoraden** med hjälp av ikonen på skrivbordet.
2. Använd följande för att starta Pig-kommando:

        %pig_home%\bin\pig

    Du kommer att visas med en `grunt>` prompten.
3. Ange följande uttryck:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    Det här kommandot läser in innehållet i sample.log-fil till filen loggar. Du kan visa innehållet i filen med hjälp av följande kommando:

        DUMP LOGS;
4. Transformera data genom att använda ett reguljärt uttryck för att extrahera endast loggningsnivån från varje post:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Du kan använda **DUMPA** att visa data efter omvandlingen. I det här fallet `DUMP LEVELS;`.
5. Fortsätt tillämpar transformationer med hjälp av följande instruktioner. Använd `DUMP` att visa resultatet av transformeringen efter varje steg.

    <table>
    <tr>
    <th>Instruktion</th><th>Vad läget gör</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER nivåer av LOGLEVEL inte är null.</td><td>Tar bort rader som innehåller ett null-värde för loggningsnivån och lagrar resultatet i FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = grupp FILTEREDLEVELS av LOGLEVEL;</td><td>Grupperar rader av loggningsnivå och lagrar resultatet i GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREKVENSER = foreach GROUPEDLEVELS generera grupp som LOGLEVEL, antal (FILTEREDLEVELS. LOGLEVEL) så räknas;</td><td>Skapar en ny uppsättning som innehåller varje unik log nivån och hur många gånger den sker. Detta lagras i frekvenser</td>
    </tr>
    <tr>
    <td>RESULTATET = order frekvenser av antal desc;</td><td>Beställningar loggningsnivåer av antal (fallande) och lagrar i resultat</td>
    </tr>
</table>

6. Du kan också spara resultatet av en omvandling med hjälp av den `STORE` instruktionen. Till exempel följande kommando sparar den `RESULT` till den **/example/data/pigout** katalogen standardbehållare för lagring för klustret:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]
   > Data lagras i den angivna katalogen i filer med namnet **del nnnnn**. Om katalogen finns redan, visas ett felmeddelande.
   >
   >
   
7. Om du vill avsluta grunt-prompten anger du följande instruktion.

        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin batch-filer
Du kan också använda Pig-kommando för att köra Pig Latin som finns i en fil.

1. När du avslutar grunt-prompten, öppna **anteckningar** och skapa en ny fil med namnet **pigbatch.pig** i den **% PIG_HOME %** directory.
2. Skriv eller klistra in följande rader i den **pigbatch.pig** och spara den:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Använd följande för att köra den **pigbatch.pig** filen med hjälp av kommandot pig.

        pig %PIG_HOME%\pigbatch.pig

    När batchjobbet har slutförts bör du se följande utdata, vilket ska vara samma som när du använde `DUMP RESULT;` i föregående steg:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Sammanfattning
Som du ser kan kommandot Pig du interaktivt kör MapReduce åtgärder eller köra Pig Latin-jobb som lagras i en batchfil.

## <a id="nextsteps"></a>Nästa steg
Allmän information om Pig i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)
