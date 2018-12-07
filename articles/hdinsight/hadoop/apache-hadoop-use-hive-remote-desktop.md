---
title: Använda Apache Hive och fjärrskrivbord i HDInsight - Azure
description: Lär dig hur du ansluter till Hadoop-kluster i HDInsight med hjälp av fjärrskrivbord och köra Hive-frågor med hjälp av kommandoradsgränssnittet Hive.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 86c1d9adc3b4ff66e2f02617df551e496a5482c8
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014624"
---
# <a name="use-apache-hive-with-apache-hadoop-on-hdinsight-with-remote-desktop"></a>Använda Apache Hive med Apache Hadoop i HDInsight med fjärrskrivbord
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

I den här artikeln kommer du lära dig hur du ansluter till ett HDInsight-kluster med hjälp av fjärrskrivbord och köra Hive-frågor med hjälp av Hive-kommandoradsgränssnittet (CLI).

> [!IMPORTANT]
> Fjärrskrivbord är bara tillgängligt på HDInsight-kluster som använder Windows som operativsystem. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> För HDInsight 3.4 eller större finns i [använda Hive med HDInsight och Beeline](apache-hadoop-use-hive-beeline.md) information om hur du kör Hive-frågor direkt i klustret från en kommandorad.

## <a id="prereq"></a>Förhandskrav
För att slutföra stegen i den här artikeln behöver du följande:

* Ett kluster för Windows-baserade HDInsight (Hadoop på HDInsight)
* En klientdator som kör Windows 10, Windows 8 eller Windows 7

## <a id="connect"></a>Ansluta med fjärrskrivbord
Aktivera Fjärrskrivbord för HDInsight-klustret och sedan ansluta till den genom att följa anvisningarna i [Anslut till HDInsight-kluster med RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Använd kommandot Hive
När du har anslutit till skrivbordet för HDInsight-kluster kan du använda följande steg för att arbeta med Hive:

1. Från HDInsight-skrivbordet startar den **Hadoop-kommandoraden**.
2. Ange följande kommando för att starta Hive-CLI:

        %hive_home%\bin\hive

    När CLI har startats visas Hive CLI-prompten: `hive>`.
3. Med hjälp av CLI anger du följande instruktioner för att skapa en ny tabell med namnet **log4jLogs** med exempeldata:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Dessa instruktioner utför följande åtgärder:

   * **DROP TABLE**: tar bort tabellen och datafilen om tabellen redan finns.
   * **Skapa extern tabell**: skapar en ny ”externa” tabell i Hive. Externa tabeller lagra endast tabelldefinitionen i Hive (data finns kvar i den ursprungliga platsen).

     > [!NOTE]
     > Externa tabeller bör användas när du förväntar dig att underliggande data uppdateras av en extern källa (till exempel en automatiserade uppladdningen) eller av en annan MapReduce-åtgärd, men du vill alltid Hive-frågor för att använda den senaste informationen.
     >
     > Tar bort en extern tabell har **inte** ta bort data, endast tabelldefinitionen.
     >
     >
   * **RAD FORMAT**: talar om Hive hur informationen har formaterats. I det här fallet avgränsas fälten i varje logg med ett blanksteg.
   * **LAGRADE AS TEXTFILE plats**: talar om för Hive-data lagras (katalogen/exempeldata) och som lagras som text.
   * **Välj**: väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]**. Detta bör returnera ett värde av **3** eftersom det finns tre rader som innehåller det här värdet.
   * **INPUT__FILE__NAME som '%.log'** -talar om Hive som vi bör endast returnera data från filer som slutar på. log. Detta begränsar sökningen till sample.log-fil som innehåller data, och håller den från att returnera data från andra exempel filer som inte matchar schemat som vi definierade.
4. Använd följande instruktioner för att skapa en ny ”intern” tabell med namnet **felvillkoren**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Dessa instruktioner utför följande åtgärder:

   * **SKAPA tabellen IF NOT finns**: skapar en tabell om den inte redan finns. Eftersom den **externa** nyckelord används inte, det här är en intern tabell som lagras i datalagret Hive och hanteras helt av Hive.

     > [!NOTE]
     > Till skillnad från **externa** tabeller, släppa en intern tabell även tar bort underliggande data.
     >
     >
   * **LAGRADE AS ORC**: lagrar data i optimerad raden (ORC) kolumnformat. Det här är ett mycket optimerade och effektiv format för att lagra Hive-data.
   * **INFOGA SKRIVA ÖVER... Välj**: väljer rader från den **log4jLogs** tabellen som innehåller **[fel]**, infogar data till den **felvillkoren** tabell.

     Kontrollera att endast rader som innehåller **[fel]** i kolumnen t4 sparades till den **felvillkoren** tabell, använder du följande instruktion för att returnera alla rader från **felvillkoren**:

       Välj * från felvillkoren;

     Tre raderna med data ska returneras, som innehåller alla **[fel]** i kolumnen t4.

## <a id="summary"></a>Sammanfattning
Som du ser ger kommandot Hive ett enkelt sätt att köra Hive-frågor på ett HDInsight-kluster, övervaka jobbstatus och hämta utdata interaktivt.

## <a id="nextsteps"></a>Nästa steg
Allmän information om Hive i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Om du använder Tez med Hive finns i följande dokument för felsökningsinformation:

* [Använda Tez-Användargränssnittet på Windows-baserade HDInsight](../hdinsight-debug-tez-ui.md)
* [Använda Ambari Tez-vyn på Linux-baserat HDInsight](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
