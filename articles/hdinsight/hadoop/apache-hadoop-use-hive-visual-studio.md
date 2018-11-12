---
title: Hive med Data Lake (Hadoop)-verktyg för Visual Studio - Azure HDInsight
description: Lär dig hur du använder Data Lake tools för Visual Studio för att köra Apache Hive-frågor med Apache Hadoop på Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 4cc2012e40fad9e1343804149cdffaa3a1ab9db4
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011093"
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Köra Hive-frågor med hjälp av Data Lake-verktyg för Visual Studio

Lär dig hur du använder Data Lake-verktyg för Visual Studio för att fråga som Apache Hive. Data Lake-verktyg kan du enkelt skapa, skicka och övervaka Hive-frågor till Hadoop på Azure HDInsight.

## <a id="prereq"></a>Förhandskrav

* Ett kluster i Azure HDInsight (Hadoop på HDInsight)

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (någon av följande versioner):

    * Visual Studio Community 2013/Professional/Premium/Ultimate med uppdatering 4

    * Visual Studio 2015 (alla versioner)

    * Visual Studio 2017 (alla versioner)

* HDInsight tools för Visual Studio eller Azure Data Lake tools för Visual Studio. Se [Kom igång med Visual Studio Hadoop-verktyg för HDInsight](apache-hadoop-visual-studio-tools-get-started.md) information om hur du installerar och konfigurerar verktygen.

## <a id="run"></a> Köra Hive-frågor med Visual Studio

1. Öppna **Visual Studio** och välj **New** > **projekt** > **Azure Data Lake**  >   **HIVE** > **Hive programmet**. Ange ett namn för det här projektet.

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

   * `DROP TABLE`: Om tabellen finns det här uttrycket tar bort den.

   * `CREATE EXTERNAL TABLE`: Skapar en ny ”externa” tabell i Hive. Externa tabeller kan du bara lagra tabelldefinitionen i Hive (data finns kvar i den ursprungliga platsen).

     > [!NOTE]
     > Externa tabeller som ska användas när du förväntar dig att underliggande data uppdateras av en extern källa. Till exempel ett MapReduce-jobb eller Azure-tjänst.
     >
     > Tar bort en extern tabell har **inte** ta bort data, endast tabelldefinitionen.

   * `ROW FORMAT`: Visar Hive hur informationen har formaterats. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

   * `STORED AS TEXTFILE LOCATION`: Visar Hive att data lagras i katalogen/exempeldata och att den lagras som text.

   * `SELECT`: Välj en uppräkning av alla rader där kolumnen `t4` innehåller värdet `[ERROR]`. Det här uttrycket returnerar värdet `3` eftersom det finns tre rader som innehåller det här värdet.

   * `INPUT__FILE__NAME LIKE '%.log'` -Talar om Hive vi bör endast returnera data från filer som slutar på. log. Den här satsen begränsar sökningen till sample.log-fil som innehåller data.

3. I verktygsfältet väljer du den **HDInsight-kluster** som du vill använda för den här frågan. Välj **skicka** att köra instruktionerna som ett Hive-jobb.

   ![Skicka stapel](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. Den **Hive-jobbsammanfattning** öppnas och visar information om det pågående jobbet. Använd den **uppdatera** länk för att uppdatera Jobbinformationen om, tills den **jobbstatus** ändras till **slutförd**.

   ![jobbsammanfattning visar ett slutfört jobb](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Använd den **Jobbutdata** länken för att visa utdata för jobbet. Den visar `[ERROR] 3`, vilket är värdet som returneras av den här frågan.

6. Du kan också köra Hive-frågor utan att skapa ett projekt. Med hjälp av **Server Explorer**, expandera **Azure** > **HDInsight**, högerklicka på din server för HDInsight och välj sedan **Skriv en Hive-fråga** .

7. I den **temp.hql** dokumentet som visas, Lägg till följande HiveQL-instruktioner:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Dessa instruktioner utför följande åtgärder:

   * `CREATE TABLE IF NOT EXISTS`: Skapar en tabell om den inte redan finns. Eftersom den `EXTERNAL` nyckelord används inte, den här instruktionen skapar en intern tabell. Interna tabeller lagras i datalagret Hive och hanteras av Hive.

     > [!NOTE]
     > Till skillnad från `EXTERNAL` tabeller, släppa en intern tabell även tar bort underliggande data.

   * `STORED AS ORC`: Lagrar data i optimerad raden (ORC) kolumnformat. ORC är ett mycket optimerade och effektiv format för att lagra Hive-data.

   * `INSERT OVERWRITE ... SELECT`: Väljer rader från den `log4jLogs` tabellen som innehåller `[ERROR]`, infogar data till den `errorLogs` tabell.

8. I verktygsfältet väljer du **skicka** jobbet ska köras. Använd den **jobbstatus** att fastställa att jobbet har slutförts.

9. Om du vill kontrollera att jobbet har skapats i tabellen genom att använda **Server Explorer** och expandera **Azure** > **HDInsight** > ditt HDInsight-kluster >  **Hive-databaser** > **standard**. Den **felvillkoren** tabell och **log4jLogs** tabellen visas.

## <a id="nextsteps"></a>Nästa steg

Som du kan se ger HDInsight tools för Visual Studio ett enkelt sätt att arbeta med Hive-frågor på HDInsight.

Allmän information om Hive i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)

* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Mer information om HDInsight-verktyg för Visual Studio:

* [Komma igång med HDInsight tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

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
