---
title: Använda Hadoop Hive på konsolen frågan i HDInsight - Azure | Microsoft Docs
description: Lär dig använda frågan webbaserade konsolen för att köra Hive-frågor i ett HDInsight Hadoop-kluster från din webbläsare.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5ae074b0-f55e-472d-94a7-005b0e79f779
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 04a6ad67fec4145d8f9164743b08f9e105778091
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="run-hive-queries-using-the-query-console"></a>Köra Hive-frågor med hjälp av konsolen för frågan
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

I den här artikeln får du lära dig hur du använder HDInsight frågan konsolen för att köra Hive-frågor i ett HDInsight Hadoop-kluster från din webbläsare.

> [!IMPORTANT]
> Konsolen HDInsight frågan är bara tillgängligt på Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> För HDInsight 3.4 eller större finns [köra Hive-frågor i Ambari Hive-vy](apache-hadoop-use-hive-ambari-view.md) information om hur du kör Hive-frågor från en webbläsare.

## <a id="prereq"></a>Förhandskrav
Du behöver följande för att slutföra stegen i den här artikeln.

* Ett Windows-baserade HDInsight Hadoop-kluster
* En modern webbläsare

## <a id="run"></a> Köra Hive-frågor med hjälp av konsolen för frågan
1. Öppna en webbläsare och gå till **https://CLUSTERNAME.azurehdinsight.net**, där **KLUSTERNAMN** är namnet på ditt HDInsight-kluster. Om du uppmanas ange användarnamn och lösenord som du använde när du skapade klustret.
2. Med länkarna överst på sidan Välj **Hive-redigeraren**. Detta visar ett formulär som kan användas för att ange HiveQL-uttryck som du vill köra i HDInsight-klustret.

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
     > Externa tabeller ska användas när du förväntar dig underliggande data uppdateras av en extern källa (till exempel en automatisk överföring av data) eller av en annan MapReduce-åtgärd, men du vill använda Hive-frågor för att använda den senaste informationen.
     >
     > Släppa en extern tabell har **inte** ta bort data, endast tabelldefinitionen.
     >
     >
   * **RADEN FORMAT**: talar om Hive hur data ska formateras. I det här fallet avgränsas fälten i varje logg med ett blanksteg.
   * **LAGRAS AS TEXTFILE plats**: talar om Hive där data är lagras (exempel/datakatalog) och som den lagras som text
   * **Välj**: Välj en uppräkning av alla rader där kolumnen **t4** innehålla värdet **[fel]**. Detta bör returnera ett värde av **3** eftersom det finns tre rader som innehåller det här värdet.
   * **INPUT__FILE__NAME som '%.log'** -talar om Hive som vi ska bara returnera data från filer som slutar på. log. Detta begränsar sökningen till sample.log-filen som innehåller data och håller den från att returnera data från andra exempel filer som inte matchar det schema som vi har definierat.
3. Klicka på **skicka**. Den **jobbet Session** längst ned på sidan ska visa information för jobbet.
4. När den **Status** ändras till **slutförd**väljer **visa information om** för jobbet. På informationssidan om den **Jobbutdata** innehåller `[ERROR]    3`. Du kan använda den **hämta** under den här fält att hämta en fil som innehåller resultatet av jobbet.

## <a id="summary"></a>Sammanfattning
Som du ser ger frågan konsolen ett enkelt sätt att köra Hive-frågor i ett HDInsight-kluster, övervaka jobbstatus och hämta utdata.

Mer information om hur du använder Hive-fråga konsolen för att köra Hive-jobb, Välj **komma igång** längst upp i konsolen fråga sedan använda de exempel som tillhandahålls. Varje prov går igenom hur du använder Hive för att analysera data, inklusive beskrivningar av HiveQL-instruktioner som används i exemplet.

## <a id="nextsteps"></a>Nästa steg
Allmän information om Hive i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Om du använder Tez med Hive finns i följande dokument för felsökningsinformation:

* [Använda Tez-Användargränssnittet på Windows-baserade HDInsight](../hdinsight-debug-tez-ui.md)
* [Använd vyn Ambari Tez på Linux-baserat HDInsight](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

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
