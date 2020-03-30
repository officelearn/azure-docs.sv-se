---
title: Apache Hadoop & Visual Studio Data Lake Tools - Azure HDInsight
description: Lär dig hur du installerar och använder DataSjöverktyg för Visual Studio för att ansluta till Apache Hadoop-kluster i Azure HDInsight och kör sedan Hive-frågor.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272791"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Använda DataSjöverktyg för Visual Studio för att ansluta till Azure HDInsight och köra Apache Hive-frågor

Lär dig hur du använder Microsoft Azure Data Lake och Stream Analytics Tools för Visual Studio (kallas även DataSjöverktyg) för att ansluta till [Apache Hadoop-kluster i Azure HDInsight](apache-hadoop-introduction.md) och skicka Hive-frågor.  

Mer information om hur du använder HDInsight finns i [Komma igång med HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Mer information om hur du ansluter till ett Apache Storm-kluster finns i [Utveckla C#-topologier för Apache Storm med hjälp av datasjöverktygen för Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Data Lake Tools för Visual Studio kan användas för att komma åt Azure Data Lake Analytics och HDInsight. Information om Data Lake Tools finns i [Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Krav

Om du vill slutföra den här artikeln och använda DataSjöverktyg för Visual Studio behöver du följande:

* Ett Azure HDInsight-kluster. Information om hur du skapar ett HDInsight-kluster finns i [Komma igång med att använda Apache Hadoop i Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Om du vill köra interaktiva Apache Hive-frågor behöver du ett [HDInsight Interactive](../interactive-query/apache-interactive-query-get-started.md) Query-kluster.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). [Visual Studio Community-utgåvan](https://visualstudio.microsoft.com/vs/community/) är gratis. Instruktionerna som visas här gäller [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake-verktyg för Visual Studio  

Följ lämpliga instruktioner för att installera Data Lake Tools för din version av Visual Studio:

- För Visual Studio 2017 eller Visual Studio 2019:

    Under Installationen av Visual Studio kontrollerar du att du inkluderar **Azure-utvecklingsarbetsbelastningen** eller **datalagrings- och bearbetningsarbetsbelastningen.**  

    För befintliga Visual Studio-installationer går du till IDE-menyraden och väljer **Verktyg** > **Hämta verktyg och funktioner** för att öppna Visual Studio Installer. På fliken **Arbetsbelastningar** väljer du minst **Azure-utvecklingsarbetsbelastningen** (under **Web & Cloud**) eller **datalagrings- och bearbetningsarbetsbelastningen** (under **Andra verktygsuppsättningar**).

  ![Val av arbetsbelastning, Visual Studio Installer](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- För Visual Studio 2015:

    [Ladda ner Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Välj den version av Data Lake Tools som matchar din version av Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Uppdatera datasjöverktyg för Visual Studio  

Kontrollera sedan att du uppdaterar DataSjöverktyg till den senaste versionen.

1. Öppna Visual Studio.

2. Välj Fortsätt utan **kod**i **startfönstret** .

3. I menyraden Visual Studio IDE väljer du **Tillägg** > **Hantera tillägg**.

4. Expandera noden **Uppdateringar** i dialogrutan **Hantera tillägg.**

5. Om listan över tillgängliga uppdateringar innehåller **Azure Data Lake och Stream Analytic Tools**väljer du den. Välj sedan knappen **Uppdatera.** När dialogrutan **Hämta och installera** visas och försvinner lägger Visual Studio till tillägget Azure Data Lake och Stream **Analytic Tools** i uppdateringsschemat.

6. Stäng alla Visual Studio-fönster. Dialogrutan **VSIX Installer** visas.

7. Välj **Licens** för att läsa licensvillkoren och välj sedan **Stäng** för att återgå till dialogrutan **VSIX Installer.**

8. Välj **Ändra**. Installationen av tilläggsuppdateringen börjar. Efter ett tag ändras dialogrutan för att visa att den är klar med ändringar. Välj **Stäng**och starta sedan om Visual Studio för att slutföra installationen.

> [!NOTE]  
> Du kan endast använda Data Lake Tools i version 2.3.0.0 eller senare för att ansluta till interaktiva frågekluster och köra interaktiva Hive-frågor.

## <a name="connect-to-azure-subscriptions"></a>Ansluta till Azure-prenumerationer

Du kan använda DataSjöverktyg för Visual Studio för att ansluta till dina HDInsight-kluster, utföra vissa grundläggande hanteringsåtgärder och köra Hive-frågor.

> [!NOTE]  
> Information om hur du ansluter till ett allmänt Hadoop-kluster finns i [Så här skriver och skickar du Hive-frågor med Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

Så här ansluter du till din Azure-prenumeration:

1. Öppna Visual Studio.

2. Välj Fortsätt utan **kod**i **startfönstret** .

3. I IDE-menyraden väljer du **Visa** > **serverutforskaren**.

4. Högerklicka på **Azure**i **Server Explorer,** välj **Anslut till Microsoft Azure-prenumeration**och slutför autentiseringsprocessen. Expandera **Azure** > **HDInsight** från **Server Explorer**för att visa en lista över befintliga HDInsight-kluster.

5. Om du inte har några kluster skapar du ett med hjälp av Azure-portalen, Azure PowerShell eller HDInsight SDK. Mer information finns [i Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Klusterlista för HDInsight, Server Explorer, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Expandera ett HDInsight-kluster. Klustret innehåller noder för **Hive-databaser**, ett standardlagringskonto, eventuella ytterligare länkade lagringskonton och **Hadoop Service Log**. Du kan expandera entiteterna ytterligare.

När du har anslutit till din Azure-prenumeration kan du utföra följande uppgifter.

### <a name="connect-to-azure-from-visual-studio"></a>Ansluta till Azure från Visual Studio

Så här ansluter du till Azure-portalen från Visual Studio:

1. Expandera **Azure** > **HDInsight** i **Server Explorer**och välj ditt kluster.

2. Högerklicka på ett HDInsight-kluster och välj **Hantera kluster i Azure-portalen**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Ge frågor och feedback från Visual Studio

Så här ställer du frågor och/eller ger feedback från Visual Studio:

1. Välj **Azure** > **HDInsight**i Server Explorer .

2. Högerklicka på **HDInsight** och välj antingen **MSDN Forum** för att ställa frågor, eller **Ge feedback** för att ge feedback.

## <a name="link-to-or-edit-a-cluster"></a>Länka till eller redigera ett kluster

> [!NOTE]
> För närvarande den enda typen av HDInsight kluster du kan länka till är en Hive typ.

Så här länkar du ett HDInsight-kluster:

1. Högerklicka på **HDInsight**och välj sedan **Länka ett HDInsight-kluster** för att visa dialogrutan Länka ett **HDInsight-kluster.**

2. Ange en **anslutningsadress** i formuläret *\://\<https-klusternamn&nbsp;>.azurehdinsight.net*. **Klusternamnet** fylls automatiskt i med klusternamnsdelen av webbadressen när du går till ett annat fält. Ange sedan ett **användarnamn** och **lösenord**och välj **Nästa**.

    ![Länka ett kluster, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Välj **Slutför**. Om klusterlänkningen lyckas visas klustret under **HDInsight-noden.**

Om du vill uppdatera ett länkat kluster högerklickar du på klustret och väljer **Redigera**. Du kan sedan uppdatera klusterinformationen.

![Redigera ett länkat kluster, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Utforska länkade resurser
Från Server Explorer kan du se standardkontot för lagring och eventuella länkade lagringskonton. Om du expanderar standardkontot för lagring kan du se containrarna på lagringskontot. Standardlagringskontot och standardcontainern är markerade.

![Datasjöverktyg för länkade resurser i Visual Studio i Utforskaren](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Högerklicka på en behållare och välj **Visa behållare** om du vill visa behållarens innehåll. När du har öppnat en behållare kan du använda verktygsfältsknapparna för att **uppdatera** innehållslistan, **Ladda upp Blob,** **Ta bort markerade blobbar**, **Öppna Blob**och hämta **(Spara som)** valda blobbar.

![Containerlista och blob-åtgärder, HDInsight-kluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Kör interaktiva Apache Hive-frågor
[Apache Hive](https://hive.apache.org) är en infrastruktur för informationslager som bygger på Hadoop. Hive används för att sammanfatta data, frågor och analys. Du kan använda Data Lake Tools för Visual Studio för att köra Hive-frågor från Visual Studio. Mer information om Hive finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md).

[Interaktiv fråga i Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) använder [Hive på LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) i Apache Hive 2.1. Interaktiv fråga ger interaktivitet till komplexa, informationslager-stil frågor på stora, lagrade datauppsättningar. Köra Hive-frågor på interaktiv fråga är mycket snabbare än traditionella Hive-batchjobb. 

> [!NOTE]  
> Du kan bara köra interaktiva Hive-frågor när du ansluter till ett kluster med en [interaktiv HDInsight-fråga](../interactive-query/apache-interactive-query-get-started.md).

Du kan också använda Data Lake Tools för Visual Studio för att se vad som finns i ett Hive-jobb. Data Lake Tools för Visual Studio samlar in och hämtar Yarn-loggarna för specifika Hive-jobb.

Välj **Azure** > **HDInsight** i **Server Explorer**och välj ditt kluster.  Den här noden är startpunkten i **Server Explorer** som avsnitten ska följa.

### <a name="view-hivesampletable"></a>Visa hivesampletable

Alla HDInsight-kluster har en standardexempel i Hive-tabellen som kallas `hivesampletable`.  

Välj **Hive-databaser** > **som standardprogramvara** > för**hivesampletable**i klustret.

- Så här `hivesampletable` visar du schemat:

    Expandera **hivesampletable**. Namnen och datatyperna `hivesampletable` för kolumnerna visas.

- Så här `hivesampletable` visar du data:

    Högerklicka på **hivesampletable**och välj **Visa topp 100 rader**. Listan med 100 resultat visas i fönstret **Hivesampletable.** Den här åtgärden motsvarar att köra följande Hive-fråga med hjälp av Hive ODBC-drivrutinen:

    `SELECT * FROM hivesampletable LIMIT 100`

    Du kan anpassa radantalet genom att ändra **Antal rader.** Du kan välja 50, 100, 200 eller 1000 rader i listrutan.

### <a name="create-hive-tables"></a>Skapa Hive-tabeller
Du kan använda det grafiska användargränssnittet eller Hive-frågor för att skapa en Hive-tabell. Information om hur du använder Hive-frågor finns i [Skapa och köra Hive-frågor](#create-and-run-hive-queries).

1. Välj **Hive-databaser** > **som standard**i klustret.

2. Högerklicka på **standard**och välj **Skapa tabell**.

3. Konfigurera tabellen.

4. Välj knappen **Skapa tabell** om du vill skicka jobbet, som skapar den nya Hive-tabellen.

    ![Skapa tabellfönster, Hive, HDInsight-kluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Skapa och köra Hive-frågor
Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor
* Skapa ett Hive-program

#### <a name="create-an-ad-hoc-query"></a>Skapa en ad hoc-fråga

Så här skapar och kör du en ad hoc-fråga:

1. Högerklicka på klustret där du vill köra frågan och välj **Skriv en Hive-fråga**.  

2. Ange en Hive-fråga.

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Om du till `SELECT * FROM`exempel skriver listar IntelliSense alla föreslagna tabellnamn. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF.

    ![IntelliSense exempel 1, Hive ad hoc-fråga, HDInsight-kluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense exempel 2, Hive ad-hoc-fråga, HDInsight-kluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.

    Här är en exempelfråga som du kan använda:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Välj körningsläge:

    * **Interaktiva**  

        I den första listrutan väljer du **Interaktiv**och väljer sedan **Kör**.

        ![Interaktivt läge, Hive ad hoc-fråga, HDInsight-kluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        I den första listrutan väljer du **Batch**och väljer sedan **Skicka** (eller väljer listrutan bredvid **Skicka** och väljer **Avancerat**).

        ![Batch-läge, Hive ad hoc-fråga, HDInsight-kluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Om du väljer alternativet avancerad skicka visas dialogrutan **Skicka skript.** Konfigurera **jobbnamn,** **argument,** **ytterligare konfigurationer**och **statuskatalog** för skriptet.

        ![Dialogrutan Skicka skript, Hive ad hoc-fråga, HDInsight-kluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Du kan inte skicka batchar till interaktiva frågekluster.  Du måste använda interaktivt läge.

#### <a name="create-a-hive-application"></a>Skapa ett Hive-program

Så här skapar och kör du en Hive-lösning:

1. Välj **Arkiv** > **nytt** > **projekt**på menyraden .

2. I fönstret **Skapa ett nytt projekt** markerar du sökrutan och skriver **Hive**. Välj sedan **Hive-programmet** och välj **Nästa**.

3. I fönstret **Konfigurera det nya projektet** anger du ett **projektnamn,** markerar eller skapar **projektplatsen**och väljer sedan **Skapa**.

    ![Nya Hive-program, Konfigurera ditt nya projektfönster, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. Gå till **Solution Explorer** och dubbelklicka på **Script.hql** för att öppna skriptet.

### <a name="view-job-summary-and-output"></a>Visa jobbsammanfattning och utdata

Jobbsammanfattningen varierar något mellan **batch-** och **interaktivt** läge.

![Sammanfattning av Hive-jobbfönster, batch- och interaktivt läge, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Använd ikonen **Uppdatera** för att uppdatera statusen tills jobbstatusen ändras till **Färdig**.  

* För jobbinformationen från **batch-läge** väljer du länkarna längst ned för att se **jobbfrågan,** **jobbutdata**eller **jobbloggen**eller för **att visa garnloggar**.

* Information om jobbinformation från **interaktivt** läge finns i **rutorna Utdata** och **HiveServer2-utdata.**

    ![Hive interaktiva jobbutdata, HDInsight-kluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Visa jobbdiagram

För närvarande visas jobbdiagram endast för Hive-jobb som använder Tez som exekveringsmotor.  Information om hur du aktiverar Tez finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md).  Se även [Använd Apache Tez istället för Karta Minska](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Om du vill visa alla operatorer i hörnet dubbelklickar du på projektdiagrammets hörn. Du kan också peka på en specifik operator för att se mer information om den.

Även om Tez anges som exekveringsmotorn kanske jobbdiagrammet inte visas om ingen Tez-program startas.  Den här situationen kan uppstå eftersom jobbet inte innehåller DML-satser eller på grund av att DML-satser kan returneras utan att starta ett Tez-program. Till exempel `SELECT * FROM table1` startar inte Tez-programmet.

![Apache Hive jobb graf, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Visa information om körning av uppgifter

I jobbdiagrammet kan du välja **Information om aktivitetskörning** för att få strukturerad och visualiserad information för Hive-jobb. Du kan också få mer jobbinformation. Om det uppstår prestandaproblem kan du använda vyn för att få mer information om problemet. Du kan till exempel hämta information om hur varje aktivitet fungerar och detaljerad information om varje aktivitet (dataläsning/skrivning, schema/start/sluttid med mera). Använd informationen för att finjustera jobbkonfigurationer eller systemarkitektur baserat på den visualiserade informationen.

![Fönstret Vy för aktivitetskörning, Visual Studio-verktyg för Datasjö](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Visa Hive-jobb

Du kan visa jobbfrågor, jobbutdata, jobbloggar och Yarn-loggar för Hive-jobb.

I den senaste versionen av verktygen kan du se vad som finns i dina Hive-jobb genom att samla in och visa Yarn-loggar. En Yarn-logg kan hjälpa dig att undersöka prestandaproblem. Mer information om hur HDInsight samlar in garnloggar finns i [Tillgång till Apache Hadoop YARN-programloggar](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Så här visar du Hive-jobb:

1. Högerklicka på ett HDInsight-kluster och välj **Visa jobb**.

    ![Visa jobb, Apache Hive, HDInsight-kluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    En lista visas över de Hive-jobb som körts på klustret.  

2. Välj ett jobb. I fönstret Sammanfattning av **Hive-jobb** väljer du en av följande länkar:
    - **Jobbfråga**
    - **Jobbutdata**
    - **Jobblogg**  
    - **Garn Logg**

## <a name="run-apache-pig-scripts"></a>Kör Apache Pig skript

1. Välj **Arkiv** > **nytt** > **projekt**på menyraden .

2. Markera sökrutan i **startfönstret** och ange **Gris**. Välj sedan **Grisprogram och** välj **Nästa**.

3. I fönstret **Konfigurera det nya projektet** anger du ett **projektnamn**och väljer eller skapar en **plats** för projektet. Välj sedan **Skapa**.

4. Dubbelklicka på **Script.pig** i fönstret IDE **Solution Explorer** för att öppna skriptet.

## <a name="feedback-and-known-issues"></a>Feedback och kända problem

* Ett problem där resultat som har startats med null-värden inte visas har åtgärdats. Om du är blockerad för det här problemet kan du kontakta supportgruppen.

* Det HQL-skript som Visual Studio skapar är kodat, beroende på användarens lokala regioninställning. Skriptet kan inte köras korrekt om du laddar upp skriptet till ett kluster som en binär fil.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur använder Data Lake Tools för Visual Studio för att ansluta till HDInsight-kluster från Visual Studio. Du har också lärt dig hur du kör en Hive-fråga. Mer information finns i dessa artiklar:

* [Köra Apache Hive-frågor med hjälp av Data Lake Tools för Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md)
* [Skapa Apache Hadoop-kluster – mall](apache-hadoop-linux-tutorial-get-started.md)
* [Skicka Apache Hadoop jobb i HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analysera Twitter-data med Apache Hive och Apache Hadoop på HDInsight](../hdinsight-analyze-twitter-data-linux.md)
