---
title: Använda Apache Hive på Frågekonsolen i HDInsight - Azure
description: Lär dig mer om att använda Frågekonsolen webbaserade för att köra Apache Hive-frågor på ett HDInsight Hadoop-kluster från din webbläsare.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 1e638bd348b7a5272dd8bfbe25aa841f38a51b9a
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409708"
---
# <a name="run-apache-hive-queries-using-the-query-console"></a>Kör Apache Hive-frågor med Frågekonsolen
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

I den här artikeln får lära du dig att använda Frågekonsolen HDInsight för att köra Apache Hive-frågor på ett HDInsight Hadoop-kluster från din webbläsare.

> [!IMPORTANT]  
> HDInsight Frågekonsolen är endast tillgänglig på Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> För HDInsight 3.4 eller större finns i [kör Apache Hive-frågor i Ambari Hive-vyn](apache-hadoop-use-hive-ambari-view.md) information om hur du kör Hive-frågor från en webbläsare.

## <a id="prereq"></a>Förhandskrav
Du behöver följande för att slutföra stegen i den här artikeln.

* Ett Windows-baserade HDInsight Hadoop-kluster
* En modern webbläsare

## <a id="run"></a> Kör Apache Hive-frågor med Frågekonsolen
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

   * **TA BORT TABELLEN**: Tar bort tabellen och datafilen om tabellen redan finns.
   * **SKAPA EXTERN TABELL**: Skapar en ny ”externa” tabell i Hive. Externa tabeller lagra endast tabelldefinitionen i Hive; data finns kvar i den ursprungliga platsen.

     > [!NOTE]  
     > Externa tabeller bör användas när du förväntar dig att underliggande data uppdateras av en extern källa (till exempel en automatiserade uppladdningen) eller av en annan MapReduce-åtgärd, men du vill alltid Hive-frågor för att använda den senaste informationen.
     >
     > Tar bort en extern tabell har **inte** ta bort data, endast tabelldefinitionen.
     >
     >
   * **RAD FORMAT**: Beskriver hur informationen har formaterats Hive. I det här fallet avgränsas fälten i varje logg med ett blanksteg.
   * **LAGRAS SOM TEXTFILE PLATS**: Anger var data lagras Hive (katalogen/exempeldata) och att den lagras som text
   * **VÄLJ**: Välj en uppräkning av alla rader där kolumnen **t4** innehålla värdet **[fel]**. Detta bör returnera ett värde av **3** eftersom det finns tre rader som innehåller det här värdet.
   * **INPUT__FILE__NAME som '%.log'** -talar om Hive som vi bör endast returnera data från filer som slutar på. log. Detta begränsar sökningen till sample.log-fil som innehåller data, och håller den från att returnera data från andra exempel filer som inte matchar schemat som vi definierade.
3. Klicka på **Skicka**. Den **jobbet Session** längst ned på sidan visas information för jobbet.
4. När den **Status** fältändringar till **slutförd**väljer **visa information om** för jobbet. På informationssidan om den **Jobbutdata** innehåller `[ERROR]    3`. Du kan använda den **hämta** under den här fält att hämta en fil som innehåller utdata från jobbet.

## <a id="summary"></a>Sammanfattning
Som du kan se är det enkelt att köra Hive-frågor i ett HDInsight-kluster, övervaka jobbstatus och hämta utdata i Frågekonsolen.

Mer information om hur du använder Hive-fråga konsolen för att köra Hive-jobb, Välj **komma igång** högst upp i Frågekonsolen, sedan använda de exempel som tillhandahålls. Varje exempel beskriver hur du använder Hive för att analysera data, inklusive beskrivningar av HiveQL-instruktioner som används i exemplet.

## <a id="nextsteps"></a>Nästa steg
Allmän information om Hive i HDInsight:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Om du använder Tez med Hive finns i följande dokument för felsökningsinformation:

* [Använda Apache Tez-Användargränssnittet på Windows-baserade HDInsight](../hdinsight-debug-tez-ui.md)
* [Använd Apache Ambari Tez-vyn på Linux-baserat HDInsight](../hdinsight-debug-ambari-tez-view.md)

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
