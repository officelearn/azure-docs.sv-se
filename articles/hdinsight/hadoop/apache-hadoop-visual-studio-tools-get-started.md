---
title: Anslut till Azure HDInsight med hjälp av Data Lake Tools för Visual Studio | Microsoft Docs
description: Få reda på hur du installerar och använder Data Lake Tools för Visual Studio för att ansluta till ett Hadoop-kluster i Azure HDInsight och sedan köra en Hive-fråga.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: afd40d75bb9c5fd3170a4da215925244994d7749
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>Använd Data Lake Tools för Visual Studio för att ansluta till Azure HDInsight och köra Hive-frågor

Få reda på hur du använder Data Lake Tools för Visual Studio (det kallas även Azure Data Lake och Stream Analytics Tools för Visual Studio) för att ansluta till ett Hadoop-kluster i [Azure HDInsight](../hdinsight-hadoop-introduction.md) och köra Hive-frågor. 

Mer information om hur du använder HDInsight finns i [Introduktion till HDInsight](../hdinsight-hadoop-introduction.md) och [Komma igång med HDInsight](apache-hadoop-linux-tutorial-get-started.md). 

Mer information om hur du ansluter till ett Storm-kluster finns i [Utveckla C#-topologier för Apache Storm med HDInsight med hjälp av Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Data Lake Tools för Visual Studio kan användas för att komma åt Azure Data Lake Analytics och HDInsight. Information om Data Lake Tools finns i [Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna genomföra den här självstudien och använda Data Lake Tools för Visual Studio behöver du följande objekt:

* Ett Azure HDInsight-kluster. Information om hur du skapar ett HDInsight-kluster finns i [Komma igång med Hadoop i Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). För att kunna köra interaktiva Hive-frågor behöver du ett kluster med en [interaktiv HDInsight-fråga](../interactive-query/apache-interactive-query-get-started.md).
* En dator med Visual Studio 2017, 2015 eller 2013 installerat.
    
    > [!NOTE]
    > För närvarande finns Data Lake Tools för Visual Studio bara tillgängligt på engelska.
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>Installera eller uppdatera Data Lake Tools för Visual Studio

### <a name="install-data-lake-tools"></a>Installera Data Lake Tools

Data Lake-verktyg installeras som standard för Visual Studio 2017. För tidigare versioner av Visual Studio kan du installera Data Lake Tools med hjälp av [installationsprogrammet för webbplattformen](https://www.microsoft.com/web/downloads/platform.aspx). Välj den version av Data Lake Tools som matchar din version av Visual Studio. 

### <a name="install-visual-studio"></a>Installera Visual Studio

Om du inte har Visual Studio installerat kan du använda [installationsprogrammet för webbplattformen](https://www.microsoft.com/web/downloads/platform.aspx) för att installera de senaste versionerna av Visual Studio Community och Azure SDK:

![Skärmbild av installationsprogrammet för webbplattformen för Data Lake Tools för Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Använd installationsprogrammet för webbplattformen för att installera Data Lake Tools för Visual Studio")

### <a name="update-the-tools"></a>Uppdatera verktygen

1. Öppna Visual Studio.
2. På menyn **Verktyg** väljer du **Tillägg och uppdateringar**.
3. Expandera **Uppdateringar**, välj **Azure Data Lake och Stream Analytics Tools** (om de är installerade).

> [!NOTE]
>
> Du kan endast använda Data Lake Tools i version 2.3.0.0 eller senare för att ansluta till interaktiva frågekluster och köra interaktiva Hive-frågor.

## <a name="connect-to-azure-subscriptions"></a>Ansluta till Azure-prenumerationer
Du kan använda Data Lake Tools för Visual Studio för att ansluta till dina HDInsight-kluster, utföra vissa grundläggande hanteringsåtgärder och köra Hive-frågor.

> [!NOTE]
> Information om hur du ansluter till ett allmänt Hadoop-kluster finns i [Skriva och skicka Hive-frågor med Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).
> 
> 

Så här ansluter du till din Azure-prenumeration:

1. Öppna Visual Studio.
2. Gå till menyn **Visa** och välj **Server Explorer**.
3. I Server Explorer expanderar du **Azure** och expanderar sedan **HDInsight**.
   
   > [!NOTE]
   > Fönstret **Uppgiftslista för HDInsight** ska vara öppet. Om du inte ser fönstret går du till menyn **Visa** och väljer **Andra fönster** och sedan **Fönster för uppgiftslista för HDInsight**.  
   > 
   > 
4. Ange autentiseringsuppgifterna för din Azure-prenumeration och välj sedan **Logga in**. Autentisering krävs endast om du inte tidigare har anslutit till Azure-prenumerationen från Visual Studio på den här datorn.
5. I Server Explorer kan du se en lista över befintliga HDInsight-kluster. Om du inte har några kluster kan du skapa ett med hjälp av Azure-portalen, Azure PowerShell eller HDInsight SDK. Mer information finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).
   
   ![Skärmbild av Data Lake Tools för Visual Studio-klusterlista i Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Data Lake Tools för Visual Studio-klusterlista i Server Explorer")
6. Expandera ett HDInsight-kluster. Du kommer att se **Hive-databaser**, ett standardkonto för lagring, länkade lagringskonton och **loggen för Hadoop-tjänsten**. Du kan expandera entiteterna ytterligare.

När du har anslutit till din Azure-prenumeration kan du utföra följande uppgifter.

Så här ansluter du till Azure-portalen från Visual Studio:

1. Välj **Azure** > **HDInsight** i Server Explorer.
2. Högerklicka på ett HDInsight-kluster och välj sedan **Hantera kluster i Azure-portalen**.

Så här ställer du frågor och ger feedback från Visual Studio:

1. På menyn **Verktyg** väljer du **HDInsight**.
2. Om du vill ställa frågor väljer du **MSDN-forum**. Om du vill ge feedback väljer du **Ge feedback**.

## <a name="explore-linked-resources"></a>Utforska länkade resurser
Från Server Explorer kan du se standardkontot för lagring och eventuella länkade lagringskonton. Om du expanderar standardkontot för lagring kan du se behållarna på lagringskontot. Standardlagringskontot och standardbehållaren är markerade. Du kan också högerklicka på någon av behållarna för att visa innehållet.

![Skärmbild av Data Lake Tools för Visual Studio-lista med länkade resurser i Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Lista med länkade resurser")

När du har öppnat en behållare kan du använda följande knappar för att överföra, ta bort och hämta blobar:

![Skärmbild av Data Lake Tools för Visual Studio-blobåtgärder i Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Ladda upp, ta bort och ladda ned blobar i Server Explorer")

## <a name="run-interactive-hive-queries"></a>Köra interaktiva Hive-frågor
[Apache Hive](http://hive.apache.org) är en infrastruktur för informationslager som bygger på Hadoop. Hive används för att sammanfatta data, frågor och analys. Du kan använda Data Lake Tools för Visual Studio för att köra Hive-frågor från Visual Studio. Mer information om Hive finns i [Använda Hive med HDInsight](hdinsight-use-hive.md).

Den [interaktiva frågan](../interactive-query/apache-interactive-query-get-started.md) använder [Hive på LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) i Apache Hive 2.1. Interaktiva frågor ger interaktivitet till komplexa informationslagerfrågor i stora, lagrade datauppsättningar. Det går mycket snabbare att köra Hive-frågor på interaktiva frågor jämfört med traditionella Hive-batchjobb. Mer information finns i [Köra Hive batchjobb](#run-hive-batch-jobs).

> [!NOTE]
>
> Du kan bara köra interaktiva Hive-frågor när du ansluter till ett kluster med en [interaktiv HDInsight-fråga](../interactive-query/apache-interactive-query-get-started.md).

Du kan också använda Data Lake Tools för Visual Studio för att se vad som finns i ett Hive-jobb. Data Lake Tools för Visual Studio samlar in och hämtar Yarn-loggarna för specifika Hive-jobb.

### <a name="view-hivesampletable"></a>Visa **hivesampletable**
Alla HDInsight-kluster har en standardtabell med Hive-exempel med namnet hivesampletable. Den här Hive-tabellen visar hur du listar Hive-tabeller, visar tabellscheman och listar raderna i Hive-tabellen.

Så här listar du Hive-tabeller och visar Hive-tabellscheman:

1. Visa ett tabellschema genom att gå till **Server Explorer** och välja alternativet **Azure** > **HDInsight**. Välj klustret och välj sedan **Hive-databaser** > **Standard** > **hivesampletable**.
2. Högerklicka på **hivesampletable** och klicka sedan på **Visa de 100 översta raderna** för att lista raderna. Det motsvarar att köra följande Hive-fråga med hjälp av Hive ODBC-drivrutinen:
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   Du kan anpassa antalet rader.
   
   ![Skärmbild av HDInsight Hive Visual Studio-schemafråga](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Hive-fråga, resultat")

### <a name="create-hive-tables"></a>Skapa Hive-tabeller
Du kan använda det grafiska användargränssnittet eller Hive-frågor för att skapa en Hive-tabell. Information om hur du använder Hive-frågor finns i [Köra Hive-frågor](#run.queries).

Så här skapar du en Hive-tabell:

1. I **Server Explorer** väljer du **Azure** > **HDInsight-kluster**. Välj HDInsight-kluster och sedan **Hive-databaser**.
2. Högerklicka på **standard** och välj sedan **Skapa tabell**.
3. Konfigurera tabellen.  
4. Klicka på **Skapa tabell** för att skicka jobbet som skapar den nya Hive-tabellen.
   
    ![Skärmbild för HDInsight Visual Studio Tools, skapa Hive-tabell](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Skapa Hive-tabell")

### <a name="run.queries"></a>Validera och köra Hive-frågor
Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor
* Skapa ett Hive-program

Skapa, verifiera och köra ad-hoc-frågor:

1. I **Server Explorer** väljer du **Azure** > **HDInsight-kluster**.
2. Högerklicka på det kluster där du vill köra frågan och välj sedan **Skriv en Hive-fråga**.  
3. Ange Hive-frågorna. 

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Om du till exempel skriver **SELECT * FROM** visar IntelliSense en lista över alla föreslagna tabellnamn. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF.
   
    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 1](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")
   
    ![Skärmbild av HDInsight Visual Studio Tools, IntelliSense exempel 2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")
   
   > [!NOTE]
   > IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.
   > 
   
4. (Valfritt) Välj **Verifiera skriptet** för att kontrollera om det finns syntaxfel i skriptet.
   
    ![Skärmbild av Data Lake Tools för lokal verifiering av Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "Verifiera skript")
5. Välj **Skicka** eller **Skicka (avancerat)**. Om du väljer det avancerade alternativet för att skicka konfigurerar du **Jobbnamn**, **Argument**, **Ytterligare konfigurationer** och **Statuskatalog** för skriptet:
   
    ![Skärmbild av HDInsight Hadoop Hive-fråga](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Skicka frågor")
   
    När du har skickat jobbet visas fönstret **Hive-jobbsammanfattning**.
   
    ![Skärmbild av en sammanfattning av en HDInsight Hadoop Hive-fråga](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Hive-jobbsammanfattning")
6. Du kan använda knappen **Uppdatera** för att uppdatera statusen tills jobbets status har ändrats till **Slutfört**.
7. Klicka på länkarna längst ned om du vill visa **Jobbfråga**, **Jobbutdata**, **Jobblogg** eller **Yarn-logg**.

Så här skapar och kör du en Hive-lösning:

1. På menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
2. Välj **HDInsight** i den vänstra fönsterrutan. I den mellersta fönsterrutan väljer du **Hive-program**. Ange egenskaper och välj sedan **OK**.
   
    ![Skärmbild av ett nytt Hive-projekt i HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Skapa Hive-program från Visual Studio")
3. Gå till **Solution Explorer** och dubbelklicka på **Script.hql** för att öppna skriptet.
4. Om du vill verifiera Hive-skriptet väljer du knappen **Verifiera skript**. Du kan även högerklicka på skriptet i Hive-redigeraren och välja **Verifiera skript** på snabbmenyn.

### <a name="view-hive-jobs"></a>Visa Hive-jobb
Du kan visa jobbfrågor, jobbutdata, jobbloggar och Yarn-loggar för Hive-jobb. Mer information finns på föregående skärmbild.

I den senaste versionen av verktygen kan du se vad som finns i dina Hive-jobb genom att samla in och visa Yarn-loggar. En Yarn-logg kan hjälpa dig att undersöka prestandaproblem. Mer information om hur HDInsight samlar in Yarn-loggar finns i [Access HDInsight Application Logs Programmatically](../hdinsight-hadoop-access-yarn-app-logs.md) (Komma åt HDInsight-programloggar via programmering).

Så här visar du Hive-jobb:

1. I **Server Explorer** expanderar du **Azure** och sedan **HDInsight**.
2. Högerklicka på ett HDInsight-kluster och välj sedan **Visa jobb**. En lista visas över de Hive-jobb som körts på klustret.  
3. Välj ett jobb. I fönstret **Hive-jobbsammanfattning** väljer du något av följande:
    - **Jobbfråga**
    - **Jobbutdata**
    - **Jobblogg**  
    - **Yarn-logg**
   
    ![Skärmdump av HDInsight Visual Studio Tools, visa Hive-jobb](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Visa Hive-jobb")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Snabbare Hive-sökvägsexekvering via HiveServer2
> [!NOTE]
> Den här funktionen fungerar bara i ett kluster i HDInsight-version 3.2 eller senare.
 
Data Lake Tools för Visual Studio används för att skicka Hive-jobb via [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (även kallat Templeton). Metoden för att skicka Hive-jobb tog lång tid att returnera jobbdetaljer och felinformation.

Du kan lösa det här prestandaproblemet genom att köra Data Lake Tools för Visual Studio Hive-jobb direkt i klustret via HiveServer2 och kringgå RDP/SSH.

Förutom bättre prestanda kan du med den här metoden även visa Hive i Apache Tez-diagram och i uppgiftsinformationen.

I HDInsight-kluster av version 3.2 eller senare visas knappen **Kör via HiveServer2**:

![Skärmbild av Data Lake Tools för Visual Studio, körning via HiveServer2-alternativet](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "Kör Hive-frågor via HiveServer2")

Du kan också se loggarna strömmas tillbaka i realtid. Du kan också se jobbdiagram om Hive-frågan körs i Tez.

![Skärmbild av Data Lake Tools för Visual Studio, snabbare sökväg för Hive-körning med HiveServer2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Visa jobbdiagram")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>Köra frågor via HiveServer2 jämfört med att skicka frågor via WebHCat

Även om det finns många prestandafördelar med att köra frågor via HiveServer2 finns det också flera begränsningar för den här metoden. Vissa begränsningar gör att metoden inte lämpar sig för användning i produktion. 

I följande tabell visas skillnaden mellan att köra frågor via HiveServer2 och skicka frågor via WebHCat:

|  | Köra via HiveServer2 | Skicka via WebHCat |
| --- | --- | --- |
| Köra frågor |Eliminerar pålägget i WebHCat (som startar ett MapReduce-jobb med namnet "TempletonControllerJob"). |Om en fråga körs via WebHCat startar WebHCat MapReduce-jobbet, vilket medför en ytterligare fördröjning. |
| Dataströmmen loggas tillbaka |I nära realtid. |Körningsloggar för jobbet är bara tillgängliga när jobbet har slutförts. |
| Visa jobbhistorik |Om en fråga körs via HiveServer2 bevaras inte dess jobbhistorik (jobblogg, jobbutdata). Programmet kan visas i Yarn-användargränssnittet med begränsad information. |Om en fråga körs via WebHCat bevaras dess jobbhistorik (jobblogg, jobbutdata). Du kan visa jobbhistoriken med hjälp av Visual Studio, HDInsight SDK eller PowerShell. |
| Stäng fönster |Körning via HiveServer2 görs *synkront*. Om fönstren stängs avbryts körningen av frågan. |Skicka via WebHCat sker *asynkront*. Du kan skicka frågan via WebHCat och sedan stänga Visual Studio. Du kan gå tillbaka och se resultaten när som helst. |

### <a name="tez-hive-job-performance-graph"></a>Tez-diagram över Hive-jobbprestanda
I Data Lake Tools för Visual Studio kan du se prestandadiagram för Hive-jobb som körs i Tez-körningsmotorn. Information om hur du aktiverar Tez finns i [Använda Hive i HDInsight](hdinsight-use-hive.md). 

När du skickat ett Hive-jobb i Visual Studio visar Visual Studio diagrammet när jobbet har slutförts. Du kan behöva klicka på knappen **Uppdatera** för att den senaste jobbstatusen ska visas.

> [!NOTE]
> Den här funktionen är bara tillgänglig i ett kluster i HDInsight-version 3.2.4.593 eller senare. Funktionen fungerar bara på slutförda jobb. Du måste också skicka jobbet via WebHCat för att använda den här funktionen. Följande bild visas när du kör frågan via HiveServer2: 
> 
> ![Skärmdump av Hadoop Hive Tez-prestandadiagram](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "Jobbstatus")

För att du ska förstå Hive-frågan bättre har Hive Operator-vyn lagts till i den här versionen. Dubbelklicka på hörnen i jobbdiagrammet för att visa alla operatorer innanför hörnen. Du kan också peka på en specifik operator för att se mer information om den.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Vy för uppgiftskörning för Hive-jobb på Tez
Vyn för uppgiftskörning för Hive-jobb på Tez kan användas för att hämta strukturerad och visualiserad information för Hive-jobb. Du kan också hämta mer jobbinformation. Om det uppstår prestandaproblem kan du använda vyn för att få mer information om problemet. Du kan till exempel få information om hur varje uppgift fungerar och detaljerad information om varje uppgift (lästa/skrivna data, schema/starttid/sluttid och så vidare). Använd informationen för att finjustera jobbkonfigurationer eller systemarkitektur baserat på den visualiserade informationen.

![Skärmbild av Data Lake Tools för Visual Studio, vy för uppgiftskörning](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Vy för uppgiftskörning")

## <a name="run-hive-batch-jobs"></a>Köra Hive-batchjobb
Det kan ta tid att testa Hive-skript mot ett HDInsight-kluster (med undantag för klustret för interaktiv fråga). Processen kan ta flera minuter eller längre. Data Lake Tools för Visual Studio kan verifiera Hive-skript lokalt utan att ansluta till ett aktivt kluster. Mer information om att köra interaktiva frågor finns i [Köra interaktiva Hive-frågor](#run-interactive-hive-queries).

Du kan använda Data Lake Tools för Visual Studio för att se vad som finns i Hive-jobb genom att samla in och visa Yarn-loggarna för specifika Hive-jobb.

Om du vill veta mer om hur du kör Hive-batchjobb kan du läsa avsnittet [Köra interaktiva Hive-frågor](#run-interactive-hive-queries). Informationen i avsnittet gäller vid körning av Hive-batchjobb med längre tider.

## <a name="run-pig-scripts"></a>Köra Pig-skript
Data Lake Tools för Visual Studio har stöd för att skapa och skicka Pig-skript till HDInsight-kluster. Skapa först ett Pig-projekt från en mall. Skicka sedan skriptet till HDInsight-kluster.

## <a name="feedback-and-known-issues"></a>Feedback och kända problem
* För närvarande visas resultaten från HiveServer2 som vanlig text, vilket inte är idealiskt. Microsoft arbetar med att åtgärda detta.
* Ett problem där resultat som har startats med null-värden inte visas har åtgärdats. Om du är blockerad för det här problemet kan du kontakta supportgruppen.
* Det HQL-skript som Visual Studio skapar är kodat, beroende på användarens lokala regioninställning. Skriptet kan inte köras korrekt om du laddar upp skriptet till ett kluster som en binär fil.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur använder Data Lake Tools för Visual Studio för att ansluta till HDInsight-kluster från Visual Studio. Du har också lärt dig hur du kör en Hive-fråga. Mer information finns i dessa artiklar:

* [Använda Hadoop Hive i HDInsight](hdinsight-use-hive.md)
* [Komma igång med Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Skicka Hadoop-jobb i HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analysera Twitter-data med Hadoop i HDInsight](../hdinsight-analyze-twitter-data.md)

