---
title: Apache Hive & datasjöverktyg för Visual Studio – Azure HDInsight
description: Lär dig hur du använder datasjöverktygen för Visual Studio för att köra Apache Hive-frågor med Apache Hadoop på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687806"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Köra Apache Hive-frågor med hjälp av Data Lake Tools för Visual Studio

Lär dig hur du använder verktygen Datasjö för Visual Studio för att fråga Apache Hive. Med datasjöverktygen kan du enkelt skapa, skicka och övervaka Hive-frågor till Apache Hadoop på Azure HDInsight.

## <a name="prerequisites"></a>Krav

* En Apache Hadoop kluster på HDInsight. Information om hur du skapar det här objektet finns [i Skapa Apache Hadoop-kluster i Azure HDInsight med hjälp av Resource Manager-mallen](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). I stegen i den här artikeln används Visual Studio 2019.

* HDInsight-verktyg för Visual Studio- eller Azure Data Lake-verktyg för Visual Studio. Information om hur du installerar och konfigurerar verktygen finns i [Installera datasjöverktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Köra Apache Hive-frågor med Visual Studio

Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor.
* Skapa ett Hive-program.

### <a name="create-an-ad-hoc-hive-query"></a>Skapa en ad hoc Hive-fråga

Ad hoc-frågor kan köras i antingen **batch-** eller **interaktivt** läge.

1. Starta **Visual Studio** och välj Fortsätt utan **kod**.

2. I **Server Explorer**högerklickar du på **Azure**, väljer Anslut till **Microsoft Azure-prenumeration...** och slutför inloggningsprocessen.

3. Expandera **HDInsight**högerklickar du på det kluster där du vill köra frågan och väljer sedan **Skriv en Hive-fråga**.

4. Ange följande hive-fråga:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Välj **Kör**. Körningsläget är som standard **interaktivt**.

    ![Kör interaktiv Hive-fråga, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Om du vill köra samma fråga i **batchläge** växlar du listrutan från **Interaktiv** till **Batch**. Körningsknappen ändras från **Kör** till **Skicka**.

    ![Skicka batch Hive-fråga, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Om du till `SELECT * FROM`exempel skriver listar IntelliSense alla föreslagna tabellnamn. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF. IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.

7. I frågeverktygsfältet (området under frågefliken och ovanför frågetexten) väljer du antingen **Skicka**eller väljer rullpilen **bredvid Skicka** och väljer **Avancerat** i rullgardinsmenyn. Om du väljer det senare alternativet

8. Om du har markerat alternativet avancerat skicka konfigurerar du **Jobbnamn,** **Argument,** **Ytterligare konfigurationer**och **statuskatalog** i dialogrutan **Skicka skript.** Välj sedan **Skicka**.

    ![Dialogrutan Skicka skript, HDInsight Hadoop Hive-fråga](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Skapa ett Hive-program

Så här kör du en Hive-fråga genom att skapa ett Hive-program:

1. Öppna **Visual Studio**.

2. Välj Skapa ett **nytt projekt**i **startfönstret** .

3. I fönstret **Skapa ett nytt projekt** anger du *Hive*i rutan **Sök efter mallar** . Välj sedan **Hive-programmet** och välj **Nästa**.

4. I fönstret **Konfigurera det nya projektet** anger du ett **projektnamn,** väljer eller skapar en **plats** för det nya projektet och väljer sedan **Skapa**.

5. Öppna **filen Script.hql** som skapas med det här projektet och klistra in följande HiveQL-satser:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Dessa uttalanden gör följande åtgärder:

    * `DROP TABLE`: Tar bort tabellen om den finns.

    * `CREATE EXTERNAL TABLE`: Skapar en ny "extern" tabell i Hive. Externa tabeller lagrar bara tabelldefinitionen i Hive. (Data finns kvar på den ursprungliga platsen.)

        > [!NOTE]  
        > Externa tabeller bör användas när du förväntar dig att underliggande data ska uppdateras av en extern källa, till exempel ett MapReduce-jobb eller en Azure-tjänst.
        >
        > Om du släpper en extern tabell tas **inte** data bort, bara tabelldefinitionen.

    * `ROW FORMAT`: Berättar för Hive hur data formateras. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

    * `STORED AS TEXTFILE LOCATION`: Talar om för Hive att data lagras i *exempel/datakatalogen* och att de lagras som text.

    * `SELECT`: Markerar ett antal rader där `t4` kolumnen innehåller `[ERROR]`värdet . Den här utdraget `3`returnerar värdet för , eftersom tre rader innehåller det här värdet.

    * `INPUT__FILE__NAME LIKE '%.log'`: Talar om för Hive att endast returnera data från filer som slutar i .log. Den här satsen begränsar sökningen till *exempelfilen* som innehåller data.

6. Välj det HDInsight-kluster som du vill använda för den här frågan i verktygsfältet frågefil (som har ett liknande utseende som ad hoc-frågeverktygsfältet). Ändra sedan **Interaktivt** till **Batch** (om det behövs) och välj **Skicka** för att köra utdragen som ett Hive-jobb.

   **Hive-jobbsammanfattningen** visas och visar information om det jobb som körs. Använd länken **Uppdatera** för att uppdatera jobbinformationen tills **jobbstatusen** ändras till **Slutförd**.

   ![Slutförd sammanfattning av Hive-jobb, Hive-program, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Välj **Jobbutdata** om du vill visa utdata för det här jobbet. Den `[ERROR] 3`visar , vilket är det värde som returneras av den här frågan.

### <a name="additional-example"></a>Ytterligare exempel

Följande exempel bygger på `log4jLogs` tabellen som skapades i föregående procedur, [Skapa ett Hive-program](#create-a-hive-application).

1. Högerklicka på klustret i **Server Explorer**och välj Skriv **en Hive-fråga**.

2. Ange följande hive-fråga:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Dessa uttalanden gör följande åtgärder:

    * `CREATE TABLE IF NOT EXISTS`: Skapar en tabell om den inte redan finns. Eftersom `EXTERNAL` nyckelordet inte används skapas en intern tabell i den här uttrycket. Interna tabeller lagras i Hive-informationslagret och hanteras av Hive.

        > [!NOTE]  
        > Till `EXTERNAL` skillnad från tabeller tas även underliggande data om du släpper en intern tabell.

    * `STORED AS ORC`: Lagrar data i orc-format *(optimized row columnar).* ORC är ett mycket optimerat och effektivt format för lagring av Hive-data.

    * `INSERT OVERWRITE ... SELECT`: Markerar rader från `log4jLogs` tabellen `[ERROR]`som innehåller och infogar `errorLogs` sedan data i tabellen.

3. Ändra **interaktivt** till **Batch** om det behövs och välj sedan **Skicka**.

4. Om du vill kontrollera att jobbet har skapat tabellen går du till **Server Explorer** och expanderar **Azure** > **HDInsight**. Expandera ditt HDInsight-kluster och expandera sedan **Hive-databaser som** > **standard**. Tabellen **errorLogs** och **log4jLogs-tabellen** visas.

## <a name="next-steps"></a>Nästa steg

Som du kan se är HDInsight-verktygen för Visual Studio ett enkelt sätt att arbeta med Hive-frågor på HDInsight.

* Allmän information om Hive i HDInsight finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md)

* Mer information om andra sätt du kan arbeta med Hadoop på HDInsight finns [i Använd MapReduce i Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)

* Mer information om HDInsight-verktygen för Visual Studio finns i[Använda DataSjöverktyg för Visual Studio för att ansluta till Azure HDInsight och köra Apache Hive-frågor](apache-hadoop-visual-studio-tools-get-started.md)
