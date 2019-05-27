---
title: Apache Hive med Data Lake (Apache Hadoop) tools för Visual Studio - Azure HDInsight
description: Lär dig hur du använder Data Lake tools för Visual Studio för att köra Apache Hive-frågor med Apache Hadoop på Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861604"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Kör Apache Hive-frågor med hjälp av Data Lake-verktyg för Visual Studio

Lär dig hur du använder Data Lake-verktyg för Visual Studio för att fråga som Apache Hive. Data Lake-verktyg kan du enkelt skapa, skicka och övervaka Hive-frågor till Apache Hadoop på Azure HDInsight.

## <a id="prereq"></a>Förhandskrav

* Ett Apache Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (någon av följande versioner):

    * Visual Studio 2015, 2017 (alla versioner)
    * Visual Studio Community 2013/Professional/Premium/Ultimate med uppdatering 4

* HDInsight tools för Visual Studio eller Azure Data Lake tools för Visual Studio. Se [Kom igång med Visual Studio Hadoop-verktyg för HDInsight](apache-hadoop-visual-studio-tools-get-started.md) information om hur du installerar och konfigurerar verktygen.

## <a id="run"></a> Kör Apache Hive-frågor med Visual Studio

Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor
* Skapa ett Hive-program

### <a name="ad-hoc"></a>Ad hoc

Ad hoc-frågor kan köras antingen **Batch** eller **interaktiv** läge.

1. Öppna **Visual Studio**.

2. Från **Server Explorer**, gå till **Azure** > **HDInsight**.

3. Expandera **HDInsight**, och högerklicka på klustret där du vill köra frågan och välj sedan **Skriv en Hive-fråga**.

4. Ange följande hive-fråga:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Välj **Kör**. Observera att körningsläge standardvärdet är **interaktiv**.

    ![Skärmbild på att köra interaktiva Hive-frågor](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Att köra samma fråga i **Batch** läge, växla nedrullningsbara listan från **interaktiv** till **Batch**. Observera att knappen körning ändras från **kör** till **skicka**.

    ![Skärmbild av att skicka en hive-fråga](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Exempel: Om du skriver `SELECT * FROM`, IntelliSense en lista över alla föreslagna tabellnamn. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF. IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.

    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 1](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 2](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Välj **Skicka** eller **Skicka (avancerat)**.

   Om du väljer det avancerade alternativet för att skicka konfigurerar du **Jobbnamn**, **Argument**, **Ytterligare konfigurationer** och **Statuskatalog** för skriptet:

    ![Skärmbild av HDInsight Hadoop Hive-fråga](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Skicka frågor")

### <a name="hive-application"></a>Hive-program

1. Öppna **Visual Studio**.

2. Från menyraden navigerar du till **filen** > **New** > **projekt**.

3. Från den **nytt projekt** fönstret går du till **mallar** > **Azure Data Lake** > **HIVE (HDInsight)**  >  **Hive programmet**. 

4. Ange ett namn för det här projektet och välj sedan **OK**.

5. Öppna den **Script.hql** filen som skapas med det här projektet och klistra in följande HiveQL-instruktioner:

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

   * `ROW FORMAT`: Beskriver hur informationen har formaterats Hive. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

   * `STORED AS TEXTFILE LOCATION`: Visar Hive att data lagras i katalogen/exempeldata och att den lagras som text.

   * `SELECT`: Välj en uppräkning av alla rader där kolumnen `t4` innehåller värdet `[ERROR]`. Det här uttrycket returnerar värdet `3` eftersom det finns tre rader som innehåller det här värdet.

   * `INPUT__FILE__NAME LIKE '%.log'` -Talar om Hive vi bör endast returnera data från filer som slutar på. log. Den här satsen begränsar sökningen till sample.log-fil som innehåller data.

6. I verktygsfältet väljer du den **HDInsight-kluster** som du vill använda för den här frågan. Välj **skicka** att köra instruktionerna som ett Hive-jobb.

   ![Skicka stapel](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. Den **Hive-jobbsammanfattning** öppnas och visar information om det pågående jobbet. Använd den **uppdatera** länk för att uppdatera Jobbinformationen om, tills den **jobbstatus** ändras till **slutförd**.

   ![jobbsammanfattning visar ett slutfört jobb](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Använd den **Jobbutdata** länken för att visa utdata för jobbet. Den visar `[ERROR] 3`, vilket är värdet som returneras av den här frågan.

### <a name="additional-example"></a>Ytterligare exempel

Det här exemplet är beroende av den `log4jLogs` tabellen som skapades i föregående steg.

1. Från **Server Explorer**, högerklicka på klustret och välj **Skriv en Hive-fråga**.

2. Ange följande hive-fråga:

    ```hql
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

3. Kör frågan i **Batch** läge.

4. Om du vill kontrollera att jobbet har skapats i tabellen genom att använda **Server Explorer** och expandera **Azure** > **HDInsight** > ditt HDInsight-kluster >  **Hive-databaser** > **standard**. Den **felvillkoren** tabell och **log4jLogs** tabellen visas.

## <a id="nextsteps"></a>Nästa steg

Som du kan se ger HDInsight tools för Visual Studio ett enkelt sätt att arbeta med Hive-frågor på HDInsight.

Allmän information om Hive i HDInsight:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)

* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Mer information om HDInsight-verktyg för Visual Studio:

* [Komma igång med HDInsight tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)