---
title: Ansluta till Apache Hadoop med Data Lake Tools för Visual Studio - Azure HDInsight
description: Lär dig hur du installerar och använder Data Lake Tools för Visual Studio för att ansluta till Apache Hadoop-kluster i Azure HDInsight och köra Hive-frågor.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: cf392bb254e38b2e07a92e87927b12e144b26f16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344075"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Använd Data Lake Tools för Visual Studio för att ansluta till Azure HDInsight och kör Apache Hive-frågor

Lär dig hur du använder [Microsoft Azure Data Lake och Stream Analytics Tools för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (kallas även för Data Lake Tools) att ansluta till Apache Hadoop-kluster i [Azure HDInsight](../hdinsight-hadoop-introduction.md) och skicka Hive-frågor.  

Mer information om hur du använder HDInsight finns i [Introduktion till HDInsight](../hdinsight-hadoop-introduction.md) och [Komma igång med HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Mer information om hur du ansluter till ett Apache Storm-kluster finns i [utveckla C# topologier för Apache Storm på HDInsight med hjälp av Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Data Lake Tools för Visual Studio kan användas för att komma åt Azure Data Lake Analytics och HDInsight. Information om Data Lake Tools finns i [Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna genomföra den här självstudien och använda Data Lake Tools för Visual Studio behöver du följande objekt:

* Ett Azure HDInsight-kluster. Om du vill skapa ett HDInsight-kluster, se [Kom igång med Apache Hadoop i Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). För att köra interaktiva frågor med Apache Hive, behöver du en [interaktiv HDInsight-fråga](../interactive-query/apache-interactive-query-get-started.md) kluster.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 eller senare).  Den [Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/) är kostnadsfri.  Se även [installera Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

  > [!IMPORTANT]  
  > Data Lake-verktyg finns inte längre stöd för Visual Studio 2013. 

## <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake-verktyg för Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017  
  Under installationen, se till att du inkluderar minst arbetsbelastningar **Azure development** eller **datalagring och bearbetning av**.  

  För befintliga installationer på menyraden, gå till **verktyg** > **hämta verktyg och funktioner...**  att öppna installationsprogrammet för Visual Studio.  Välj sedan minst arbetsbelastningar **Azure development** eller **datalagring och bearbetning av**.

  ![Skärmbild av installationsprogrammet för Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 och 2015  
  [Hämta Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Välj den version av Data Lake Tools som matchar din version av Visual Studio.  

> [!NOTE]  
> För närvarande finns Data Lake Tools för Visual Studio bara tillgängligt på engelska.

## <a name="update-data-lake-tools-for-visual-studio"></a>Uppdatera Data Lake Tools för Visual Studio  

1. Öppna Visual Studio.

2. Från menyraden navigerar du till **verktyg** > **tillägg och uppdateringar...** .

3. Från den **tillägg och uppdateringar** fönstret expanderar **uppdateringar** till vänster.

4. Om en uppdatering är tillgänglig **Azure Data Lake och Stream analysverktyg** kommer att visas i huvudfönstret.  Välj **Uppdatera**.

> [!NOTE]  
> Du kan endast använda Data Lake Tools i version 2.3.0.0 eller senare för att ansluta till interaktiva frågekluster och köra interaktiva Hive-frågor.

## <a name="connect-to-azure-subscriptions"></a>Ansluta till Azure-prenumerationer
Du kan använda Data Lake Tools för Visual Studio för att ansluta till dina HDInsight-kluster, utföra vissa grundläggande hanteringsåtgärder och köra Hive-frågor.

> [!NOTE]  
> Information om hur du ansluter till ett allmänt Hadoop-kluster finns i [Skriva och skicka Hive-frågor med Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).

Så här ansluter du till din Azure-prenumeration:

1. Öppna Visual Studio.

2. Från menyraden navigerar du till **visa** > **Server Explorer**.

3. Från Server Explorer högerklickar du på **Azure**väljer **Anslut till Microsoft Azure-prenumeration...** , och slutföra inloggningsprocessen.

4. Från Server Explorer visas en lista över befintliga HDInsight-kluster. Om du inte har några kluster kan du skapa ett med hjälp av Azure-portalen, Azure PowerShell eller HDInsight SDK. Mer information finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Skärmbild av Data Lake Tools för Visual Studio-klusterlista i Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Data Lake Tools för Visual Studio-klusterlista i Server Explorer")

5. Expandera ett HDInsight-kluster. Du kommer att se **Hive-databaser**, ett standardkonto för lagring, länkade lagringskonton och **loggen för Hadoop-tjänsten**. Du kan expandera entiteterna ytterligare.

När du har anslutit till din Azure-prenumeration kan du utföra följande uppgifter.

Så här ansluter du till Azure-portalen från Visual Studio:

1. Från Server Explorer navigerar du till **Azure** > **HDInsight** och välj ditt kluster.

2. Högerklicka på ett HDInsight-kluster och välj **hantera kluster i Azure-portalen**.

Ställ frågor och/eller ge feedback från Visual Studio:

1. Från Server Explorer navigerar du till **Azure** > **HDInsight**.

2. Högerklicka på **HDInsight** och välj antingen **MSDN-Forum** att ställa frågor, eller **ge Feedback** att ge feedback.

## <a name="explore-linked-resources"></a>Utforska länkade resurser
Från Server Explorer kan du se standardkontot för lagring och eventuella länkade lagringskonton. Om du expanderar standardkontot för lagring kan du se containrarna på lagringskontot. Standardlagringskontot och standardbehållaren är markerade. Högerklicka på någon av behållarna för att visa innehållet.

![Skärmbild av Data Lake Tools för Visual Studio-lista med länkade resurser i Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Lista med länkade resurser")

När du har öppnat en behållare kan du använda följande knappar för att överföra, ta bort och hämta blobar:

![Skärmbild av Data Lake Tools för Visual Studio-blobåtgärder i Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Ladda upp, ta bort och ladda ned blobar i Server Explorer")

## <a name="run-interactive-apache-hive-queries"></a>Köra interaktiva Apache Hive-frågor
[Apache Hive](https://hive.apache.org) är en infrastruktur för informationslager som bygger på Hadoop. Hive används för att sammanfatta data, frågor och analys. Du kan använda Data Lake Tools för Visual Studio för att köra Hive-frågor från Visual Studio. Mer information om Hive finns i [använda Apache Hive med HDInsight](hdinsight-use-hive.md).

Den [interaktiva frågan](../interactive-query/apache-interactive-query-get-started.md) använder [Hive på LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) i Apache Hive 2.1. Interaktiva frågor ger interaktivitet till komplexa informationslagerfrågor i stora, lagrade datauppsättningar. Det går mycket snabbare att köra Hive-frågor på interaktiva frågor jämfört med traditionella Hive-batchjobb. Mer information finns i kör Apache Hive-batchjobb.

> [!NOTE]  
> Du kan bara köra interaktiva Hive-frågor när du ansluter till ett kluster med en [interaktiv HDInsight-fråga](../interactive-query/apache-interactive-query-get-started.md).

Du kan också använda Data Lake Tools för Visual Studio för att se vad som finns i ett Hive-jobb. Data Lake Tools för Visual Studio samlar in och hämtar Yarn-loggarna för specifika Hive-jobb.

Från Server Explorer navigerar du till **Azure** > **HDInsight** och välj ditt kluster.  Det här är startpunkten i Server Explorer för de avsnitt som följer.

### <a name="view-hivesampletable"></a>Visa hivesampletable
Alla HDInsight-kluster har en standard exempel Hive-tabell som kallas `hivesampletable`.  

Från ditt kluster, navigerar du till **Hive-databaser** > **standard** > **hivesampletable**.

* Visa `hivesampletable` schema:  
Expandera **hivesampletable**.

* Visa `hivesampletable` data:  
Högerklicka på **hivesampletable**, och välj **visa de 100 översta raderna**.  Det motsvarar att köra följande Hive-fråga med hjälp av Hive ODBC-drivrutinen:

   `SELECT * FROM hivesampletable LIMIT 100`

  Du kan anpassa antalet rader.

  ![Skärmbild av HDInsight Hive Visual Studio-schemafråga](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Hive-fråga, resultat")

### <a name="create-hive-tables"></a>Skapa Hive-tabeller
Du kan använda det grafiska användargränssnittet eller Hive-frågor för att skapa en Hive-tabell. Information om hur du använder Hive-frågor finns i [kör Apache Hive-frågor](#run.queries).

1. Från ditt kluster, navigerar du till **Hive-databaser** > **standard**.

2. Högerklicka på **standard**, och välj **Create Table**.

3. Konfigurera tabellen som du vill.  

4. Klicka på **Skapa tabell** för att skicka jobbet som skapar den nya Hive-tabellen.

    ![Skärmbild för HDInsight Visual Studio Tools, skapa Hive-tabell](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Skapa Hive-tabell")

### <a name="run.queries"></a>Skapa och köra Hive-frågor
Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor
* Skapa ett Hive-program

Skapa och köra ad hoc-frågor:

1. Högerklicka på klustret där du vill köra frågan och välj **Skriv en Hive-fråga**.  

2. Ange Hive-frågorna.  

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Exempel: Om du skriver `SELECT * FROM`, IntelliSense en lista över alla föreslagna tabellnamn. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF.

    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 1](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")

    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.

3. Välj körningsläge:

    * **Interaktiv**  

      Se till att **interaktiv** är markerad och välj sedan **kör**.

      ![Skärmbild av frågan och kör](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      Se till att **Batch** är markerad och välj sedan **skicka**.  Om du väljer alternativet avancerade skicka konfigurera **jobbnamn**, **argument**, **ytterligare konfigurationer**, och **Statuskatalog**för skriptet.

      ![Skärmbild av fråge- och batch](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Skärmbild av HDInsight Hadoop Hive-fråga](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Skicka frågor")

      > [!NOTE]  
      > Du kan inte skicka batchar till interaktiva frågekluster.  Du måste använda interaktivt läge.

Så här skapar och kör du en Hive-lösning:

1. Från menyraden navigerar du till **filen** > **New** > **projekt...** .

2. I den vänstra rutan, gå till **installerad** > **Azure Data Lake** > **HIVE (HDInsight)**.  

3. I den mellersta fönsterrutan väljer du **Hive-program**. Ange egenskaper och välj sedan **OK**.

    ![Skärmbild av ett nytt Hive-projekt i HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Skapa Hive-program från Visual Studio")

4. Gå till **Solution Explorer** och dubbelklicka på **Script.hql** för att öppna skriptet.

### <a name="view-job-summary-and-output"></a>Visa jobbsammanfattning och utdata

Jobbsammanfattningen varierar lite mellan **Batch** och **interaktiv** läge.

![Jobbsammanfattning](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Hive-jobbsammanfattning")

Använd den **uppdatera** knappen för att uppdatera statusen tills jobbets status ändras till **slutfört**.  

* För information om återställningsjobb från **Batch** läge, klicka på länkarna längst ned för att se **Jobbfråga**, **Jobbutdata**, **jobblogg**, eller **Yarn-logg**.

* För information om återställningsjobb från **interaktiv** läge, se flikarna **utdata** och **HiveServer2 utdata**.

  ![Jobbdetaljer](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Hive jobbinformation")

### <a name="view-job-graph"></a>Visa jobbdiagram

Jobbdiagram visas för närvarande endast för Hive-jobb som använder Tez som motorn för körning.  Information om hur du aktiverar Tez finns i [använda Apache Hive i HDInsight](hdinsight-use-hive.md).  Se även [använda Apache Tez i stället för Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Dubbelklicka på hörnen i jobbdiagrammet för att visa alla operatorer innanför hörnen. Du kan också peka på en specifik operator för att se mer information om den.

Jobbdiagram kan inte visas även om Tez har angetts som motorn för körning om inget Tez-program startas.  Detta kan inträffa eftersom jobbet inte innehåller DML-instruktioner eller DML-instruktioner kan returnera utan att starta ett program i Tez. Till exempel `SELECT * FROM table1` kommer inte att starta Tez-programmet.

![Jobbdiagram](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Hive-jobbsammanfattning")


### <a name="task-execution-detail"></a>Information om körning av aktiviteten

Jobbdiagram, du kan välja **information om körning av aktiviteten** att hämta strukturerad och visualiserad information för Hive-jobb. Du kan också hämta mer jobbinformation. Om det uppstår prestandaproblem kan du använda vyn för att få mer information om problemet. Du kan till exempel få information om hur varje uppgift fungerar och detaljerad information om varje uppgift (lästa/skrivna data, schema/starttid/sluttid och så vidare). Använd informationen för att finjustera jobbkonfigurationer eller systemarkitektur baserat på den visualiserade informationen.

![Skärmbild av Data Lake Tools för Visual Studio, vy för uppgiftskörning](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Vy för uppgiftskörning")


### <a name="view-hive-jobs"></a>Visa Hive-jobb
Du kan visa jobbfrågor, jobbutdata, jobbloggar och Yarn-loggar för Hive-jobb.

I den senaste versionen av verktygen kan du se vad som finns i dina Hive-jobb genom att samla in och visa Yarn-loggar. En Yarn-logg kan hjälpa dig att undersöka prestandaproblem. Mer information om hur HDInsight samlar in Yarn-loggar finns i [Access HDInsight Application Logs Programmatically](../hdinsight-hadoop-access-yarn-app-logs-linux.md) (Komma åt HDInsight-programloggar via programmering).

Så här visar du Hive-jobb:

1. Högerklicka på ett HDInsight-kluster och välj **visa jobb**. En lista visas över de Hive-jobb som körts på klustret.  

2. Välj ett jobb. I fönstret **Hive-jobbsammanfattning** väljer du något av följande:
   - **Jobbfråga**
   - **Jobbutdata**
   - **Jobblogg**  
   - **Yarn-logg**

     ![Skärmdump av HDInsight Visual Studio Tools, visa Hive-jobb](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Visa Hive-jobb")


## <a name="run-apache-pig-scripts"></a>Kör Apache Pig-skript

1. Från menyraden navigerar du till **filen** > **New** > **projekt...** .

2. I den vänstra rutan, gå till **installerad** > **Azure Data Lake** > **Pig (HDInsight)**.  

3. I den mellersta rutan väljer **Pig program**. Ange egenskaper och välj sedan **OK**.

4. I **Solution Explorer**, dubbelklicka på **Script.pig** att öppna skriptet.

## <a name="feedback-and-known-issues"></a>Feedback och kända problem
* Ett problem där resultat som har startats med null-värden inte visas har åtgärdats. Om du är blockerad för det här problemet kan du kontakta supportgruppen.
* Det HQL-skript som Visual Studio skapar är kodat, beroende på användarens lokala regioninställning. Skriptet kan inte köras korrekt om du laddar upp skriptet till ett kluster som en binär fil.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur använder Data Lake Tools för Visual Studio för att ansluta till HDInsight-kluster från Visual Studio. Du har också lärt dig hur du kör en Hive-fråga. Mer information finns i dessa artiklar:

* [Kör Apache Hive-frågor med hjälp av Data Lake-verktyg för Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Använda Hadoop Hive i HDInsight](hdinsight-use-hive.md)
* [Komma igång med Apache Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Skicka Apache Hadoop-jobb i HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analysera Twitter-data med Apache Hadoop i HDInsight](../hdinsight-analyze-twitter-data-linux.md)

