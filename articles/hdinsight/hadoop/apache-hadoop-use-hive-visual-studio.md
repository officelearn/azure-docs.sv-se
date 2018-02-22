---
title: "Hive med Data Lake (Hadoop) verktyg för Visual Studio - Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder Data Lake-verktyg för Visual Studio för att köra Apache Hive-frågor med Apache Hadoop på Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 410c2ee581a98a44e7c3c98e680686e5a1eae34e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Köra Hive-frågor med hjälp av Data Lake-verktyg för Visual Studio

Lär dig hur du använder Data Lake-verktyg för Visual Studio för att fråga Apache Hive. Data Lake-verktyg kan du enkelt skapa, skicka och övervaka Hive-frågor till Hadoop på Azure HDInsight.

## <a id="prereq"></a>Förhandskrav

* Ett kluster i Azure HDInsight (Hadoop på HDInsight)

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (någon av följande versioner):

    * Visual Studio Community 2013/Professional/Premium/Ultimate med Update 4

    * Visual Studio 2015 (någon utgåva)

    * 2017 för Visual Studio (någon utgåva)

* HDInsight tools för Visual Studio eller Azure Data Lake-verktyg för Visual Studio. Se [komma igång med Visual Studio Hadoop-verktygen för HDInsight](apache-hadoop-visual-studio-tools-get-started.md) för information om hur du installerar och konfigurerar verktygen.

## <a id="run"></a> Köra Hive-frågor med Visual Studio

1. Öppna **Visual Studio** och välj **ny** > **projekt** > **Azure Data Lake** > **HIVE** > **Hive-program**. Ange ett namn för det här projektet.

2. Öppna den **Script.hql** filen som skapas med det här projektet och klistra in följande HiveQL-instruktioner:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    Dessa instruktioner utför följande åtgärder:

   * `DROP TABLE`: Om tabellen finns den här instruktionen tar bort den.

   * `CREATE EXTERNAL TABLE`: Skapar en ny ”externa” tabell i Hive. Externa tabeller kan du bara lagra tabelldefinitionen i Hive (data finns kvar i den ursprungliga platsen).

     > [!NOTE]
     > Externa tabeller ska användas när du förväntar dig underliggande data uppdateras av en extern källa. Till exempel en MapReduce-jobb eller Azure-tjänsten.
     >
     > Släppa en extern tabell har **inte** ta bort data, endast tabelldefinitionen.

   * `ROW FORMAT`: Anger hur data är formaterad Hive. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

   * `STORED AS TEXTFILE LOCATION`: Anger att data lagras i katalogen/exempeldata och att den är lagrad som text Hive.

   * `SELECT`: Välj en uppräkning av alla rader där kolumnen `t4` innehåller värdet `[ERROR]`. Den här instruktionen returnerar ett värde för `3` eftersom det finns tre rader som innehåller det här värdet.

   * `INPUT__FILE__NAME LIKE '%.log'` -Talar om Hive vi bör endast returnera data från filer som slutar på. log. Den här satsen begränsar sökningen till sample.log-fil som innehåller data.

3. I verktygsfältet väljer du den **HDInsight-kluster** som du vill använda för den här frågan. Välj **skicka** att köra instruktionerna som Hive-jobb.

   ![Skicka stapel](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. Den **Hive-jobbsammanfattning** visas information om jobb som körs. Använd den **uppdatera** länk för att uppdatera jobb, tills den **jobbstatus** ändras till **slutförd**.

   ![jobbsammanfattning visa slutförda jobb](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Använd den **Jobbutdata** länken om du vill visa resultatet av jobbet. Den visar `[ERROR] 3`, vilket är värdet som returneras av den här frågan.

6. Du kan också köra Hive-frågor utan att skapa ett projekt. Med hjälp av **Server Explorer**, expandera **Azure** > **HDInsight**, högerklicka på ditt HDInsight-servern och välj sedan **skriva en Hive-fråga**.

7. I den **temp.hql** dokument som visas, Lägg till följande HiveQL-instruktioner:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Dessa instruktioner utför följande åtgärder:

   * `CREATE TABLE IF NOT EXISTS`: Skapar en tabell om den inte redan finns. Eftersom den `EXTERNAL` nyckelordet används inte, skapar en intern tabell för den här instruktionen. Interna register lagras i datalagret Hive och hanteras av Hive.

     > [!NOTE]
     > Till skillnad från `EXTERNAL` tabeller, släppa en intern tabell även tar bort den underliggande data.

   * `STORED AS ORC`: Lagrar data i optimerad raden (ORC) kolumnformat. ORC är ett mycket optimerad och effektiv format för att lagra data med Hive.

   * `INSERT OVERWRITE ... SELECT`: Väljer rader från den `log4jLogs` tabellen som innehåller `[ERROR]`, infogar data till den `errorLogs` tabell.

8. I verktygsfältet väljer du **skicka** jobbet ska köras. Använd den **jobbstatus** att fastställa att jobbet har slutförts.

9. Använd för att kontrollera att jobbet skapat tabellen **Server Explorer** och expandera **Azure** > **HDInsight** > ditt HDInsight-kluster > **Hive-databaser** > **standard**. Den **errorLogs** tabell och **log4jLogs** tabellen visas.

## <a id="nextsteps"></a>Nästa steg

Som du ser tillhandahålla HDInsight tools för Visual Studio ett enkelt sätt att arbeta med Hive-frågor i HDInsight.

Allmän information om Hive i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Mer information om HDInsight tools för Visual Studio:

* [Komma igång med HDInsight tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
