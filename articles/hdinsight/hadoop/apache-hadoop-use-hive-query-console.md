---
title: Använda Hadoop Hive i Frågekonsolen i HDInsight - Azure
description: Lär dig mer om att använda Frågekonsolen webbaserade för att köra Hive-frågor på ett HDInsight Hadoop-kluster från din webbläsare.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 36bf64e44103c8568c7cb47b18e32e8f7f879e5c
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249163"
---
# <a name="run-hive-queries-using-the-query-console"></a>Köra Hive-frågor med hjälp av Frågekonsolen
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

I den här artikeln får lära du dig att använda konsolen för HDInsight-fråga för att köra Hive-frågor på ett HDInsight Hadoop-kluster från din webbläsare.

> [!IMPORTANT]
> HDInsight Frågekonsolen är endast tillgänglig på Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> För HDInsight 3.4 eller större finns i [köra Hive-frågor i Ambari Hive-vyn](apache-hadoop-use-hive-ambari-view.md) information om hur du kör Hive-frågor från en webbläsare.

## <a id="prereq"></a>Förhandskrav
Du behöver följande för att slutföra stegen i den här artikeln.

* Ett Windows-baserade HDInsight Hadoop-kluster
* En modern webbläsare

## <a id="run"></a> Köra Hive-frågor med hjälp av Frågekonsolen
1. Öppna en webbläsare och gå till **https://CLUSTERNAME.azurehdinsight.net**, där **CLUSTERNAME** är namnet på ditt HDInsight-kluster. Om du uppmanas, anger du användarnamn och lösenord som du använde när du har skapat klustret.
2. Länkar överst på sidan Välj **Hive-redigeraren**. Detta visar ett formulär som kan användas för att ange HiveQL-instruktioner som du vill köra i HDInsight-klustret.

    ![hive-redigeraren](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Ersätt texten `Select * from hivesampletable` med följande HiveQL-instruktioner:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Dessa instruktioner utför följande åtgärder:

   * **DROP TABLE**: tar bort tabellen och datafilen om tabellen redan finns.
   * **Skapa extern tabell**: skapar en ny ”externa” tabell i Hive. Externa tabeller lagra endast tabelldefinitionen i Hive; data finns kvar i den ursprungliga platsen.

     > [!NOTE]
     > Externa tabeller bör användas när du förväntar dig att underliggande data uppdateras av en extern källa (till exempel en automatiserade uppladdningen) eller av en annan MapReduce-åtgärd, men du vill alltid Hive-frågor för att använda den senaste informationen.
     >
     > Tar bort en extern tabell har **inte** ta bort data, endast tabelldefinitionen.
     >
     >
   * **RAD FORMAT**: talar om Hive hur informationen har formaterats. I det här fallet avgränsas fälten i varje logg med ett blanksteg.
   * **LAGRADE AS TEXTFILE plats**: talar om för Hive-data lagras (katalogen/exempeldata) och som lagras som text
   * **Välj**: Välj en uppräkning av alla rader där kolumnen **t4** innehålla värdet **[fel]**. Detta bör returnera ett värde av **3** eftersom det finns tre rader som innehåller det här värdet.
   * **INPUT__FILE__NAME som '%.log'** -talar om Hive som vi bör endast returnera data från filer som slutar på. log. Detta begränsar sökningen till sample.log-fil som innehåller data, och håller den från att returnera data från andra exempel filer som inte matchar schemat som vi definierade.
3. Klicka på **skicka**. Den **jobbet Session** längst ned på sidan visas information för jobbet.
4. När den **Status** fältändringar till **slutförd**väljer **visa information om** för jobbet. På informationssidan om den **Jobbutdata** innehåller `[ERROR]    3`. Du kan använda den **hämta** under den här fält att hämta en fil som innehåller utdata från jobbet.

## <a id="summary"></a>Sammanfattning
Som du kan se är det enkelt att köra Hive-frågor i ett HDInsight-kluster, övervaka jobbstatus och hämta utdata i Frågekonsolen.

Mer information om hur du använder Hive-fråga konsolen för att köra Hive-jobb, Välj **komma igång** högst upp i Frågekonsolen, sedan använda de exempel som tillhandahålls. Varje exempel beskriver hur du använder Hive för att analysera data, inklusive beskrivningar av HiveQL-instruktioner som används i exemplet.

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

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
