---
title: Apache Hadoop-och Data Lake-verktyg för Visual Studio – Azure HDInsight
description: Lär dig hur du installerar och använder Data Lake verktyg för Visual Studio för att ansluta till Apache Hadoop kluster i Azure HDInsight och sedan köra Hive-frågor.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 44a076ee6979e207ac3992f76d3b89cc188d53b8
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076302"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Använd Data Lake verktyg för Visual Studio för att ansluta till Azure HDInsight och köra Apache Hive frågor

Lär dig hur du använder [Microsoft Azure Data Lake och Stream Analytics verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (kallas även data Lake-verktyg) för att ansluta till Apache Hadoop kluster i [Azure HDInsight](../hdinsight-hadoop-introduction.md) och skicka Hive-frågor.  

Mer information om hur du använder HDInsight finns i [Introduktion till HDInsight](../hdinsight-hadoop-introduction.md) och [Komma igång med HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Mer information om hur du ansluter till ett Apache Storm-kluster finns i [utveckla C# topologier för Apache storm i HDInsight med hjälp av Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Data Lake Tools för Visual Studio kan användas för att komma åt Azure Data Lake Analytics och HDInsight. Information om Data Lake Tools finns i [Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här artikeln och använda Data Lake verktyg för Visual Studio behöver du följande objekt:

* Ett Azure HDInsight-kluster. Information om hur du skapar ett HDInsight-kluster finns i [komma igång genom att använda Apache Hadoop i Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Om du vill köra interaktiva Apache Hive frågor behöver du ett [Interactive HDInsight](../interactive-query/apache-interactive-query-get-started.md) -kluster.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 eller senare).  [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) är kostnads fri.  Se även [Installera Visual studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) och [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Det finns små gränssnitts variationer med Visual Studio 2019.

  > [!IMPORTANT]  
  > Data Lake-verktyg stöds inte längre för Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake-verktyg för Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 eller Visual Studio 2019  
  Se till att du inkluderar minst arbets belastningar för **Azure-utveckling** eller **data lagring och bearbetning**under installationen.  

  För befintliga installationer går du till meny raden och navigerar till **verktyg** > **Hämta verktyg och funktioner...** för att öppna Visual Studio Installer.  Välj sedan minst arbets belastningar för **Azure-utveckling** eller **data lagring och bearbetning**.

  ![Skärm bild av Visual Studio Installer](./media/apache-hadoop-visual-studio-tools-get-started/vs-2017-installation.png)

* Visual Studio 2013 och 2015  
  [Ladda ned data Lake verktyg](https://www.microsoft.com/download/details.aspx?id=49504). Välj den version av Data Lake Tools som matchar din version av Visual Studio.  

> [!NOTE]  
> För närvarande finns Data Lake Tools för Visual Studio bara tillgängligt på engelska.

## <a name="update-data-lake-tools-for-visual-studio"></a>Uppdatera Data Lake verktyg för Visual Studio  

1. Öppna Visual Studio.

2. I meny raden navigerar du till **verktyg** > **tillägg och uppdateringar..** ..

3. I fönstret **tillägg och uppdateringar** expanderar du **uppdateringar** till vänster.

4. Om det finns en uppdatering visas **Azure Data Lake och Stream analys verktyg** i huvud fönstret.  Välj **Uppdatera**.

> [!NOTE]  
> Du kan endast använda Data Lake Tools i version 2.3.0.0 eller senare för att ansluta till interaktiva frågekluster och köra interaktiva Hive-frågor.

## <a name="connect-to-azure-subscriptions"></a>Ansluta till Azure-prenumerationer
Du kan använda Data Lake Tools för Visual Studio för att ansluta till dina HDInsight-kluster, utföra vissa grundläggande hanteringsåtgärder och köra Hive-frågor.

> [!NOTE]  
> Information om hur du ansluter till ett allmänt Hadoop-kluster finns i [Skriva och skicka Hive-frågor med Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).

Så här ansluter du till din Azure-prenumeration:

1. Öppna Visual Studio.

2. I meny raden navigerar du till **Visa** > **Server Explorer**.

3. Från Server Explorer högerklickar du på **Azure**, väljer **Anslut till Microsoft Azure prenumeration...** och slutför inloggnings processen.

4. Från Server Explorer visas en lista över befintliga HDInsight-kluster. Om du inte har några kluster kan du skapa ett med hjälp av Azure-portalen, Azure PowerShell eller HDInsight SDK. Mer information finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Data Lake verktyg för kluster listan i Visual Studio i Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Data Lake verktyg för kluster listan i Visual Studio i Server Explorer")

5. Expandera ett HDInsight-kluster. **Hive-databaser**, ett standard lagrings konto, länkade lagrings konton och **Hadoop-tjänsteloggen** visas. Du kan expandera entiteterna ytterligare.

När du har anslutit till din Azure-prenumeration kan du utföra följande uppgifter.

Så här ansluter du till Azure-portalen från Visual Studio:

1. Gå till **Azure** > **HDInsight** från Server Explorer och välj ditt kluster.

2. Högerklicka på ett HDInsight-kluster och välj **Hantera kluster i Azure Portal [sic]** .

Så här ställer du frågor och/eller ger feedback från Visual Studio:

1. Från Server Explorer navigerar du till **Azure** > **HDInsight**.

2. Högerklicka på **HDInsight** och välj antingen **MSDN-forumet** för att ställa frågor eller **ge feedback** för att ge feedback.

## <a name="link-a-cluster"></a>Länka ett kluster
Du kan länka ett kluster genom att högerklicka på **HDInsight** och sedan välja **Länka ett HDInsight-kluster**. Ange **anslutnings-URL**, **användar namn** och **lösen ord**, klicka på **Nästa** och sedan på **Slutför**, så ska klustret visas i listan under HDInsight-noden lyckades.

![Skärm bild av dialog rutan Data Lake verktyg för länk kluster i Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Högerklicka på det länkade klustret, Välj **Redigera**, användare kan uppdatera kluster informationen. Att lägga till ett HDInsight-kluster stöder bara Hive för tillfället.

![Skärm bild av Data Lake verktyg för uppdatering av Visual Studio Link Cluster](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Utforska länkade resurser
Från Server Explorer kan du se standardkontot för lagring och eventuella länkade lagringskonton. Om du expanderar standardkontot för lagring kan du se containrarna på lagringskontot. Standardlagringskontot och standardbehållaren är markerade. Du kan också högerklicka på någon av containrarna för att visa innehållet.

![Data Lake verktyg för länkade Visual Studio-resurser i Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Lista länkade resurser")

När du har öppnat en container kan du använda följande knappar för att överföra, ta bort och hämta blobar:

![Data Lake verktyg för Visual Studio-BLOB-åtgärder i Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Ladda upp, ta bort och ladda ned blobbar i Server Explorer")

## <a name="run-interactive-apache-hive-queries"></a>Kör interaktiva Apache Hives frågor
[Apache Hive](https://hive.apache.org) är en infrastruktur för informationslager som bygger på Hadoop. Hive används för att sammanfatta data, frågor och analys. Du kan använda Data Lake Tools för Visual Studio för att köra Hive-frågor från Visual Studio. Mer information om Hive finns i [använda Apache Hive med HDInsight](hdinsight-use-hive.md).

Den [interaktiva frågan](../interactive-query/apache-interactive-query-get-started.md) använder [Hive på LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) i Apache Hive 2.1. Interaktiva frågor ger interaktivitet till komplexa informationslagerfrågor i stora, lagrade datauppsättningar. Det går mycket snabbare att köra Hive-frågor på interaktiva frågor jämfört med traditionella Hive-batchjobb. 

> [!NOTE]  
> Du kan bara köra interaktiva Hive-frågor när du ansluter till ett kluster med en [interaktiv HDInsight-fråga](../interactive-query/apache-interactive-query-get-started.md).

Du kan också använda Data Lake Tools för Visual Studio för att se vad som finns i ett Hive-jobb. Data Lake Tools för Visual Studio samlar in och hämtar Yarn-loggarna för specifika Hive-jobb.

Gå till **Azure** > **HDInsight** från Server Explorer och välj ditt kluster.  Det här är start punkten i Server Explorer för de avsnitt som ska följas.

### <a name="view-hivesampletable"></a>Visa hivesampletable
Alla HDInsight-kluster har en standardformat Hive- `hivesampletable`tabell med namnet.  

Från klustret, navigera till **Hive-databaser** > **standard** > **hivesampletable**.

* Så här `hivesampletable` visar du schema:  
Expandera **hivesampletable**.

* Så här `hivesampletable` visar du data:  
Högerklicka på **hivesampletable**och välj **visa de översta 100 raderna**.  Det motsvarar att köra följande Hive-fråga med hjälp av Hive ODBC-drivrutinen:

   `SELECT * FROM hivesampletable LIMIT 100`

  Du kan anpassa antalet rader.

  ![Skärmbild av HDInsight Hive Visual Studio-schemafråga](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Hive-fråga, resultat")

### <a name="create-hive-tables"></a>Skapa Hive-tabeller
Du kan använda det grafiska användargränssnittet eller Hive-frågor för att skapa en Hive-tabell. Information om hur du använder Hive-frågor finns i [köra apache Hive frågor](#run.queries).

1. Från klustret, navigera till **Hive-databaser** > **standard**.

2. Högerklicka på **standard**och välj **Skapa tabell**.

3. Konfigurera tabellen efter behov.  

4. Klicka på **Skapa tabell** för att skicka jobbet som skapar den nya Hive-tabellen.

    ![Skärmbild för HDInsight Visual Studio Tools, skapa Hive-tabell](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Skapa Hive-tabell")

### <a name="run.queries"></a>Skapa och köra Hive-frågor
Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor
* Skapa ett Hive-program

Skapa och kör ad hoc-frågor:

1. Högerklicka på det kluster där du vill köra frågan och välj **Skriv en Hive-fråga**.  

2. Ange följande Hive-fråga:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Om du till exempel skriver `SELECT * FROM`visas alla föreslagna tabell namn i IntelliSense. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF.

    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 1](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.

3. Välj körnings läge:

    * **Interaktiv**  

      Se till att **interaktiv** är markerat och välj sedan **Kör**.

      ![Skärm bild av fråga och köra](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

      Se till att **batch** är markerat och välj sedan **Skicka**.  Om du väljer alternativet Avancerad överföring konfigurerar du **jobbnamn**, **argument**, **ytterligare konfigurationer**och **status katalog** för skriptet.

      ![Visual Studio-fråge-och batch-alternativ](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)  

      ![Skärmbild av HDInsight Hadoop Hive-fråga](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Skicka frågor")

      > [!NOTE]  
      > Du kan inte skicka batchar till interaktiva Query-kluster.  Du måste använda interaktivt läge.

Så här skapar och kör du en Hive-lösning:

1. I meny raden navigerar du till **filen** > **nytt** > **projekt.** ...

2. I det vänstra fönstret navigerar du till **installerat** > **Azure Data Lake** > **HIVE (HDInsight)** .  

3. I den mellersta fönsterrutan väljer du **Hive-program**. Ange egenskaper och välj sedan **OK**.

    ![Skärmbild av ett nytt Hive-projekt i HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Skapa Hive-program från Visual Studio")

4. Gå till **Solution Explorer** och dubbelklicka på **Script.hql** för att öppna skriptet.

### <a name="view-job-summary-and-output"></a>Visa jobb Sammanfattning och utdata

Jobb sammanfattningen varierar något mellan **batch** -och **interaktivt** läge.

![Fliken Apache Hive jobb Sammanfattning visas](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png "Sammanfattning av Hive-jobb")

Använd knappen **Uppdatera** för att uppdatera statusen tills jobbets status ändras till **slutförd**.  

* För jobb information från **batch** -läge väljer du länkarna längst ned för att se **jobb fråga**, **jobbets utdata**, **jobb loggen**eller **garn loggen**.

* Jobb information från **interaktivt** läge finns i flikar **utdata** och **HiveServer2 utdata**.

  ![Information om Visual Studio-Apache Hive jobb](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png "Information om Hive-jobb")

### <a name="view-job-graph"></a>Visa jobb diagram

För närvarande visas jobb diagram bara för Hive-jobb som använder Tez som körnings motor.  Information om hur du aktiverar Tez finns i [använda Apache Hive i HDInsight](hdinsight-use-hive.md).  Se också [Använd Apache Tez i stället för kart minskning](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Dubbelklicka på hörnen i jobbdiagrammet för att visa alla operatorer innanför hörnen. Du kan också peka på en specifik operator för att se mer information om den.

Jobb diagrammet kanske inte visas även om Tez har angetts som körnings motor om inget Tez-program startas.  Detta kan inträffa eftersom jobbet inte innehåller DML-instruktioner eller om DML-instruktionerna kan returnera utan att starta ett Tez-program. Startar till exempel `SELECT * FROM table1` inte Tez-programmet.

![Visual Studio Apache Hive Job-diagram](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Sammanfattning av Hive-jobb")

### <a name="task-execution-detail"></a>Information om uppgifts körning

I jobb diagrammet kan du välja information om **aktivitets körning** för att få strukturerad och visualiserad information för Hive-jobb. Du kan också hämta mer jobbinformation. Om det uppstår prestandaproblem kan du använda vyn för att få mer information om problemet. Du kan till exempel få information om hur varje uppgift fungerar och detaljerad information om varje uppgift (lästa/skrivna data, schema/starttid/sluttid och så vidare). Använd informationen för att finjustera jobbkonfigurationer eller systemarkitektur baserat på den visualiserade informationen.

![Data Lake Visa fönster för Visual Studio-verktyg för uppgifts körning](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Vyn uppgifts körning")


### <a name="view-hive-jobs"></a>Visa Hive-jobb
Du kan visa jobbfrågor, jobbutdata, jobbloggar och Yarn-loggar för Hive-jobb.

I den senaste versionen av verktygen kan du se vad som finns i dina Hive-jobb genom att samla in och visa Yarn-loggar. En Yarn-logg kan hjälpa dig att undersöka prestandaproblem. Mer information om hur HDInsight samlar in Yarn-loggar finns i [Access HDInsight Application Logs Programmatically](../hdinsight-hadoop-access-yarn-app-logs.md) (Komma åt HDInsight-programloggar via programmering).

Så här visar du Hive-jobb:

1. Högerklicka på ett HDInsight-kluster och välj **Visa jobb**. En lista visas över de Hive-jobb som körts på klustret.  

2. Välj ett jobb. I fönstret **Hive-jobbsammanfattning** väljer du något av följande:
    - **Jobbfråga**
    - **Jobbutdata**
    - **Jobblogg**  
    - **Yarn-logg**

    ![Skärmdump av HDInsight Visual Studio Tools, visa Hive-jobb](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Visa Hive-jobb")


## <a name="run-apache-pig-scripts"></a>Köra apache gris-skript

1. I meny raden navigerar du till **filen** > **nytt** > **projekt.** ...

2. I det vänstra fönstret navigerar du till **installerat** > **Azure Data Lake** > **gris (HDInsight)** .  

3. I fönstret i mitten väljer du **programmet gris**. Ange egenskaper och välj sedan **OK**.

4. I **Solution Explorer**dubbelklickar du på **skript. gris** för att öppna skriptet.

## <a name="feedback-and-known-issues"></a>Feedback och kända problem
* Ett problem där resultat som har startats med null-värden inte visas har åtgärdats. Om du är blockerad för det här problemet kan du kontakta supportgruppen.
* Det HQL-skript som Visual Studio skapar är kodat, beroende på användarens lokala regioninställning. Skriptet kan inte köras korrekt om du laddar upp skriptet till ett kluster som en binär fil.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur använder Data Lake Tools för Visual Studio för att ansluta till HDInsight-kluster från Visual Studio. Du har också lärt dig hur du kör en Hive-fråga. Mer information finns i dessa artiklar:

* [Köra Apache Hive-frågor med hjälp av Data Lake Tools för Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Använda Hadoop Hive i HDInsight](hdinsight-use-hive.md)
* [Kom igång med att använda Apache Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Skicka Apache Hadoop jobb i HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analysera Twitter-data med Apache Hadoop i HDInsight](../hdinsight-analyze-twitter-data.md)

