---
title: Apache Hive med Data Lake verktyg för Visual Studio – Azure HDInsight
description: Lär dig hur du använder Data Lake verktyg för Visual Studio för att köra Apache Hive frågor med Apache Hadoop på Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 129f200bc9f61d70f4403b1154978d57e09fee26
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917486"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Kör Apache Hive frågor med hjälp av Data Lake verktyg för Visual Studio

Lär dig hur du använder Data Lake verktyg för Visual Studio för att fråga Apache Hive. Med Data Lake-verktygen kan du enkelt skapa, skicka och övervaka Hive-frågor till Apache Hadoop på Azure HDInsight.

## <a id="prereq"></a>Förhandskrav

* Ett Apache Hadoop kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (någon av följande versioner):

    * Visual Studio 2015, 2017 (vilken utgåva som helst)
    * Visual Studio 2013 community/Professional/Premium/Ultimate med uppdatering 4

* HDInsight Tools för Visual Studio eller Azure Data Lake Tools för Visual Studio. Se [Kom igång med Visual Studio Hadoop-verktyg för HDInsight](apache-hadoop-visual-studio-tools-get-started.md) för information om hur du installerar och konfigurerar verktygen.

## <a id="run"></a>Kör Apache Hive frågor med Visual Studio

Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor
* Skapa ett Hive-program

### <a name="ad-hoc"></a>Ad hoc

Ad hoc-frågor kan köras i antingen **batch** -eller **interaktivt** läge.

1. Öppna **Visual Studio**.

2. Från **Server Explorer**navigerar du till **Azure** > **HDInsight**.

3. Expandera **HDInsight**och högerklicka på det kluster där du vill köra frågan och välj sedan **Skriv en Hive-fråga**.

4. Ange följande Hive-fråga:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Välj **Kör**. Observera att körnings läget är standard för **interaktiv**.

    ![Skärmbild på att köra interaktiva Hive-frågor](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Om du vill köra samma fråga i **batchläge** växlar du den nedrullningsbara listan från **interaktiv** till **batch**. Observera att körnings knappen ändras från att **köras** till att **skickas**.

    ![Skärmbild av att skicka en hive-fråga](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Om du till exempel skriver `SELECT * FROM`visas alla föreslagna tabell namn i IntelliSense. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF. IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.

    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 1](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 2](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Välj **Skicka** eller **Skicka (avancerat)** .

   Om du väljer det avancerade alternativet för att skicka konfigurerar du **Jobbnamn**, **Argument**, **Ytterligare konfigurationer** och **Statuskatalog** för skriptet:

    ![Skärmbild av HDInsight Hadoop Hive-fråga](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png "Skicka frågor")

### <a name="hive-application"></a>Hive-program

1. Öppna **Visual Studio**.

2. I meny raden navigerar du till **filen** > **nytt** > **projekt**.

3. I fönstret **nytt projekt** navigerar du till **mallar** > **Azure Data Lake** > Hive**Hive-program** **(HDInsight)**  > . 

4. Ange ett namn för projektet och välj sedan **OK**.

5. Öppna filen **script. HQL** som skapas med det här projektet och klistra in följande HiveQL-uttryck:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Dessa instruktioner utför följande åtgärder:

   * `DROP TABLE`: Om tabellen finns, tar den här instruktionen bort den.

   * `CREATE EXTERNAL TABLE`: Skapar en ny extern tabell i Hive. Externa tabeller lagrar bara tabell definitionen i Hive (data finns kvar på den ursprungliga platsen).

     > [!NOTE]  
     > Externa tabeller bör användas när du förväntar dig att underliggande data ska uppdateras av en extern källa. Till exempel ett MapReduce-jobb eller en Azure-tjänst.
     >
     > Att släppa en extern tabell tar **inte** bort data, endast tabell definitionen.

   * `ROW FORMAT`: Anger Hive hur data formateras. I det här fallet separeras fälten i varje logg med ett blank steg.

   * `STORED AS TEXTFILE LOCATION`: Anger Hive att data lagras i exemplet/data katalogen och att de lagras som text.

   * `SELECT`: Välj ett antal rader där kolumnen `t4` innehåller värdet. `[ERROR]` Den här instruktionen returnerar värdet `3` eftersom det finns tre rader som innehåller det här värdet.

   * `INPUT__FILE__NAME LIKE '%.log'`-Anger Hive att vi bara ska returnera data från filer som slutar med. log. Den här satsen begränsar sökningen till exempel. log-filen som innehåller data.

6. I verktygsfältet väljer du det **HDInsight-kluster** som du vill använda för den här frågan. Välj **Skicka** för att köra uttrycken som ett Hive-jobb.

   ![Skicka fält](./media/apache-hadoop-use-hive-visual-studio/hdinsight-toolbar-submit.png)

7. **Sammanfattningen av Hive-jobbet** visas och visar information om det jobb som körs. Använd länken **Uppdatera** för att uppdatera jobb informationen tills **jobbets status** ändras till **slutförd**.

   ![jobb sammanfattning som visar ett slutfört jobb](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

8. Använd länken **jobbets utdata** om du vill visa utdata för jobbet. Den visas `[ERROR] 3`, vilket är det värde som returneras av den här frågan.

### <a name="additional-example"></a>Ytterligare exempel

Det här exemplet använder den `log4jLogs` tabell som skapades i föregående steg.

1. Från **Server Explorer**högerklickar du på klustret och väljer **Skriv en Hive-fråga**.

2. Ange följande Hive-fråga:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Dessa instruktioner utför följande åtgärder:

    * `CREATE TABLE IF NOT EXISTS`: Skapar en tabell om den inte redan finns. `EXTERNAL` Eftersom nyckelordet inte används skapar den här instruktionen en intern tabell. Interna tabeller lagras i Hive-datalagret och hanteras av Hive.
    
    > [!NOTE]  
    > Till skillnad `EXTERNAL` från tabeller, raderas även underliggande data när en intern tabell släpps.

    * `STORED AS ORC`: Lagrar data i optimerade rad kolumners (ORC)-format. ORC är ett mycket optimerat och effektivt format för att lagra Hive-data.
    
    * `INSERT OVERWRITE ... SELECT`: Markerar rader i `log4jLogs` tabellen som innehåller `[ERROR]`och `errorLogs` infogar sedan data i tabellen.

3. Kör frågan i **batch** -läge.

4. Verifiera att jobbet har skapats genom att använda **Server Explorer** och expandera **Azure** > **HDInsight** > ditt HDInsight-kluster > **Hive-databaser** > som**standard**. Tabellen **errorLogs** och tabellen **log4jLogs** visas.

## <a id="nextsteps"></a>Nästa steg

Som du kan se ger HDInsight-verktygen för Visual Studio ett enkelt sätt att arbeta med Hive-frågor i HDInsight.

Allmän information om Hive i HDInsight:

* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

Information om andra sätt att arbeta med Hadoop i HDInsight:

* [Använda Apache gris med Apache Hadoop på HDInsight](hdinsight-use-pig.md)

* [Använda MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)

Mer information om HDInsight Tools för Visual Studio:

* [Komma igång med HDInsight Tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)