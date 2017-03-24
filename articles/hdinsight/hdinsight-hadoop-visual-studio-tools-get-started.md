---
title: "Lär dig använda Data Lake (HDInsight) Tools för Visual Studio | Microsoft Docs"
description: "Få reda på hur du installerar och använder Data Lake-verktygen (HDInsight) för Visual Studio för att ansluta till ett Hadoop-kluster och köra en Hive-fråga."
keywords: "hadoop-verktyg, hive-fråga, visual studio"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/07/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 8bea5c13b8657be737cdc135b2d46bd0068b6b88
ms.lasthandoff: 03/07/2017


---
# <a name="get-started-using-azure-data-lake-hdinsight-tools-for-visual-studio-to-run-a-hive-query"></a>Kom igång med Azure Data Lake-verktygen (HDInsight) för Visual Studio för att köra en Hive-fråga
Lär dig hur du använder Data Lake-verktyg (HDInsight) för Visual Studio för att ansluta till HDInsight-kluster och skicka Hive-frågor. Mer information om hur du använder HDInsight finns i [Introduktion till HDInsight][hdinsight.introduction] och [Komma igång med HDInsight][hdinsight.get.started]. Mer information om hur du ansluter till ett Storm-kluster finns i [Develop C# topologies for Apache Storm on HDInsight using Visual Studio][hdinsight.storm.visual.studio.tools] (Utveckla C#-topologier för Apache Storm med HDInsight med hjälp av Visual Studio).

Data Lake-verktyg för Visual Studio kan användas för att komma åt Data Lake Analytics och HDInsight.  Se [Självstudier: Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) för information om Data Lake-verktyg.

**Krav**

För att genomföra de här självstudierna och använda Data Lake-verktygen i Visual Studio behöver du följande:

* Ett Azure HDInsight-kluster: Om du vill skapa ett finns [Komma igång med att använda Linux-baserat HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* En arbetsstation med följande programvara:
  
  * Windows 10, Windows 8.1, Windows 8 eller Windows 7.
  * Visual Studio 2013/2015/2017.
    
    > [!NOTE]
    > Data Lake-verktyget för Visual Studio medföljer för närvarande endast i den engelska versionen.
    > 
    > 

## <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake-verktyg för Visual Studio

Data Lake-verktyg installeras som standard för Visual Studio 2017. För äldre versioner kan du installera dem med hjälp av [installationsprogrammet för webbplattformen](https://www.microsoft.com/web/downloads/). Du måste välja det alternativ som matchar din version av Visual Studio. Om du inte har Visual Studio installerat kan du installera de senaste versionerna av Visual Studio Community och Azure SDK med hjälp av [installationsprogrammet för webbplattformen](https://www.microsoft.com/web/downloads/):

![Data Lake-verktyg för installationsprogrammet för Visual Studio-webbplattformen.][1]

## <a name="connect-to-azure-subscriptions"></a>Ansluta till Azure-prenumerationer
Med Data Lake-verktyg för Visual Studio kan du ansluta till dina HDInsight-kluster, utföra vissa grundläggande hanteringsåtgärder och köra Hive-frågor.

> [!NOTE]
> Information om hur du ansluter till ett allmänt Hadoop-kluster finns i [Skriva och skicka Hive-frågor med Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).
> 
> 

**Så här ansluter du till din Azure-prenumeration**

1. Öppna Visual Studio.
2. Klicka på **Server Explorer** på menyn **Visa** för att öppna Server Explorer-fönstret.
3. Expandera **Azure** och expandera därefter **HDInsight**.
   
   > [!NOTE]
   > Notera att fönstret **Uppgiftslista för HDInsight** ska vara öppet. Om du inte ser det klickar du på **Andra fönster** på menyn **Visa** och klickar sedan på **Fönster för uppgiftslista för HDInsight**.  
   > 
   > 
4. Ange autentiseringsuppgifterna för din Azure-prenumeration och klicka sedan på **Logga In**. Detta behöver du endast göra om du aldrig har anslutit till Azure-prenumerationen från Visual Studio på den här arbetsstationen.
5. I Server Explorer kan du se en lista över befintliga HDInsight-kluster. Om du inte har några kluster kan du skapa ett med hjälp av Azure-portalen, Azure PowerShell eller HDInsight SDK. Mer information finns i [Skapa HDInsight-kluster][hdinsight-create-clusters].
   
   ![Klusterlista i Server Explorer för Data Lake-verktyg för Visual Studio][5]
6. Expandera ett HDInsight-kluster. Du kommer att se **Hive-databaser**, ett standardkonto för lagring, länkade lagringskonton och **loggen för Hadoop-tjänsten**. Du kan expandera entiteterna ytterligare.

När du har anslutit till din Azure-prenumeration kommer du att kunna göra följande:

**Så här ansluter du till Azure-portalen från Visual Studio**

* I Server Explorer expanderar du **Azure** > **HDInsight**, högerklickar på ett HDInsight-kluster och klickar sedan på **Hantera kluster i Azure-portalen**.

**Så här ställer du frågor och ger feedback från Visual Studio**

* Klicka på **HDInsight** på menyn **Verktyg** och klicka sedan på **MSDN-forum** för att ställa frågor, eller klicka på **Ge feedback**.

## <a name="navigate-the-linked-resources"></a>Navigera till de länkade resurserna
Från Server Explorer kan du se standardkontot för lagring och eventuella länkade lagringskonton. Om du expanderar standardkontot för lagring kan du se behållarna på lagringskontot. Standardlagringskontot och standardbehållaren är markerade. Du kan också högerklicka på någon av behållarna för att visa innehållet.

![Klusterlista i Server Explorer för Data Lake-verktyg för Visual Studio][2]

När du har öppnat en behållare kan du använda följande knappar för att överföra, ta bort och hämta blobar:

![Bloboperationer i Server Explorer för Data Lake-verktyg för Visual Studio](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png)

## <a name="run-a-hive-query"></a>Köra en Hive-fråga
[Apache Hive][apache.hive] är en datalagerinfrastruktur som bygger på Hadoop och som används för att tillhandahålla datasammanfattning, frågor och analys. Data Lake-verktyg för Visual Studio stöder körning av Hive-frågor från Visual Studio. Mer information om Hive finns i [Använda Hive med HDInsight][hdinsight.hive].

Det tar tid att testa Hive-skript mot ett HDInsight-kluster. Det kan ta flera minuter eller ännu längre tid. Data Lake-verktyg för Visual Studio kan validera Hive-skript lokalt utan att ansluta till ett aktivt kluster.

Med Data Lake-verktyg för Visual Studio kan användarna också se vad som finns i Hive-jobbet genom att samla in och visa YARN-loggarna för specifika Hive-jobb.

### <a name="view-the-hivesampletable"></a>Visa **hivesampletable**
Alla HDInsight-kluster har en Hive-exempeltabell med namnet *hivesampletable*. Vi använder denna tabell för att visa dig hur du listar Hive-tabeller, visar tabellscheman och listar raderna i Hive-tabellen.

**Så här listar du Hive-tabeller och visar ett Hive-tabellschema**

1. Om du vill visa tabellschemat går du till **Server Explorer** och expanderar **Azure** > **HDInsight** > valfritt kluster > **Hive-databaser** > **Standard** > **hivesampletable**.
2. Högerklicka på **hivesampletable** och klicka sedan på **Visa de 100 översta raderna** för att lista raderna. Det motsvarar att köra följande Hive-fråga med hjälp av ODBC-drivrutinen:
   
     VÄLJ * FRÅN hivesampletable GRÄNS 100
   
   Du kan anpassa antalet rader.
   
   ![Data Lake-verktyg: HDInsight Hive Visual Studio-schemafråga][6]

### <a name="create-hive-tables"></a>Skapa Hive-tabeller
Du kan använda det grafiska användargränssnittet för att skapa en Hive-tabell eller använda Hive-frågor. Information om hur du använder Hive-frågor finns i [Köra Hive-frågor](#run.queries).

**Så här skapar du en Hive-tabell**

1. Gå till **Server Explorer** och expandera **Azure** > **HDInsight-kluster** ett HDInsight-kluster > **Hive-databaser** och högerklicka sedan på **Standard** och klicka på **Skapa tabell**.
2. Konfigurera tabellen.
3. Klicka på **Skapa tabell** för att skicka jobbet som skapar den nya Hive-tabellen.
   
    ![Data Lake-verktyg: HDinsight visual studio tools, skapa Hive-tabell][7]

### <a name="run.queries"></a>Validera och köra Hive-frågor
Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor
* Skapa ett Hive-program

**Skapa, verifiera och köra ad-hoc-frågor**

1. Gå till **Server Explorer**, expandera **Azure** och expandera sedan **HDInsight-kluster**.
2. Högerklicka på det kluster där du vill köra frågan och klicka sedan på **Skriv en Hive-fråga**.
3. Ange Hive-frågorna. Notera att Hive-redigeraren stöder IntelliSense. Data Lake-verktyg för Visual Studio stöder överföring av fjärrmetadata när du redigerar Hive-skript. Om du till exempel skriver "SELECT * FROM" visar IntelliSense en lista över alla föreslagna tabellnamn. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder nästan alla Hive DML-instruktioner, underfrågor och inbyggda UDF.
   
    ![Data Lake-verktyg: HDInsight Visual Studio Tools IntelliSense][13]
   
    ![Data Lake-verktyg: HDInsight Visual Studio Tools IntelliSense][14]
   
   > [!NOTE]
   > Endast metadata för kluster som valts i verktygsfältet för HDInsight föreslås.
   > 
   > 
4. (Valfritt): Klicka på **Validera skriptet** för att kontrollera om det finns syntaxfel i skriptet.
   
    ![Data Lake-verktyg: Data Lake-verktyg för lokal Visual Studio-validering][10]
5. Klicka på **Skicka** eller **Skicka (avancerat)**. Med det avancerade alternativet för att skicka konfigurerar du **Jobbnamn**, **Argument**, **Ytterligare konfigurationer** och **Statuskatalog** för skriptet:
   
    ![HDInsight Hadoop hive query][9]
   
    När du har skickat jobbet visas fönstret **Hive-jobbsammanfattning**.
   
    ![Sammanfattning av en HDInsight Hadoop Hive-fråga][8]
6. Du kan använda knappen **Uppdatera** för att uppdatera statusen tills jobbets status har ändrats till **Slutfört**.
7. Klicka på länkarna längst ned om du vill visa följande: **Jobbfråga**, **Jobbutdata**, **Jobblogg** eller **Yarn-logg**.

**Så här skapar och kör du en Hive-lösning**

1. Klicka på **Nytt** på menyn **ARKIV** och klicka sedan på **Projekt**.
2. Välj **HDInsight** i den vänstra rutan, välj **Hive-program** i mittenrutan och klicka sedan på **OK**.
   
    ![Data Lake-verktyg: HDInsight visual studio tools, nytt hive-projekt][11]
3. Gå till **Solution Explorer** och dubbelklicka på **Script.hql** för att öppna det.
4. För att validera Hive-skriptet kan du klicka på knappen **Validera skriptet** eller högerklicka på skriptet i Hive-redigeraren och sedan klicka på **Validera skriptet** på snabbmenyn.

### <a name="view-hive-jobs"></a>Visa Hive-jobb
Du kan visa jobbfrågor, jobbutdata, jobbloggar och Yarn-loggar för Hive-jobb. Mer information finns på föregående skärmbild.

Med den senaste versionen av verktyget kan du se vad som finns i dina Hive-jobb genom att samla in och visa YARN-loggar. En YARN-logg kan hjälpa dig att undersöka prestandaproblem. Mer information om hur HDInsight samlar in YARN-loggar finns i [Access HDInsight Application Logs Programmatically][hdinsight.access.application.logs] (Komma åt HDInsight-programloggar via programmering).

**Så här visar du Hive-jobb**

1. Gå till **Server Explorer**, expandera **Azure** och expandera sedan **HDInsight**.
2. Högerklicka på ett HDInsight-kluster och klicka sedan på **Visa jobb**. En lista visas över de Hive-jobb som körts på klustret.
3. Välj ett jobb i jobblistan genom att klicka på det och använd sedan fönstret **Hive-jobbsammanfattning** för att öppna **Jobbfråga**, **Jobbutdata**, **Jobblogg** eller **Yarn-logg**.
   
    ![Data Lake-verktyg: HDInsight Visual Studio Tools, visa Hive-jobb][12]

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Snabbare Hive-sökvägsexekvering via HiveServer2
> [!NOTE]
> Den här funktionen fungerar bara på HDInsight-kluster av version 3.2 eller senare.
> 
> 

Data Lake-verktyg som används för att skicka Hive-jobb via [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (även kallat Templeton). Det tog lång tid att returnera jobbdetaljer och felinformation.
För att lösa det här prestandaproblemet kör Data Lake-verktygen Hive-jobb direkt i klustret via HiveServer2 och kringgår RDP/SSH.
Förutom bättre prestanda får användarna även möjlighet att visa Hive i Tez-diagram och se uppgiftsinformation.

För HDInsight-kluster av version 3.2 eller senare visas knappen **Kör via HiveServer2**:

![Data Lake visual studio tools, kör via hiveserver2](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png)

Dessutom kan du se loggar strömmas tillbaka i realtid och visa jobbdiagram om Hive-frågan körs i Tez.

![Data Lake visual studio tools för snabb sökväg för hive-körning](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png)

**Skillnaden mellan att köra frågor via HiveServer2 och skicka frågor via WebHCat**

Även om det finns många fördelar med att köra frågor via HiveServer2 finns det också flera begränsningar. Vissa begränsningar lämpar sig inte för användning i produktion. Följande tabell visar skillnaderna:

|  | Köra via HiveServer2 | Skicka via WebHCat |
| --- | --- | --- |
| Köra frågor |Eliminerar pålägget i WebHCat (som startar ett MapReduce-jobb med namnet "TempletonControllerJob"). |Så länge en fråga körs via WebHCat startar WebHCat MapReduce-jobbet, vilket medför en ytterligare fördröjning. |
| Dataströmmen loggas tillbaka |I nära realtid. |Körningsloggar för jobbet är bara tillgängliga när jobbet har slutförts. |
| Visa jobbhistorik |Om en fråga körs via HiveServer2 bevaras inte dess jobbhistorik (jobblogg, jobbutdata). Programmet kan visas i YARN-användargränssnittet med begränsad information. |Om en fråga körs via WebHCat bevaras dess jobbhistorik (jobblogg, jobbutdata) och kan visas med hjälp av Visual Studio/HDInsight SDK/PowerShell. |
| Stäng fönster |Att köra via HiveServer2 är en "synkron" metod, så du måste hålla fönstren öppna. Om fönstren stängs avbryts körningen av frågan. |Att skicka via WebHCat är en "asynkron" metod, så du kan skicka frågan via WebHCat och stänga Visual Studio. Du kan gå tillbaka och se resultaten när som helst. |

### <a name="tez-hive-job-performance-graph"></a>Tez-diagram över Hive-jobbprestanda
Data Lake Tools stöder visning av prestandadiagram för Hive-jobb som körts av Tez-körningsmotor. Information om hur du aktiverar Tez finns i [Använda Hive i HDInsight][hdinsight.hive]. När du skickat ett Hive-jobb i Visual Studio, visar Visual Studio diagrammet när jobbet har slutförts.  Du kan behöva klicka på knappen **Uppdatera** för att den senaste jobbstatusen ska visas.

> [!NOTE]
> Den här funktionen är endast tillgänglig för HDInsight-kluster av senare version än 3.2.4.593 och fungerar bara för slutförda jobb (om du skickat jobbet via WebHCat visas det här diagrammet när du kör frågan via HiveServer2). Detta fungerar både i Windows- och Linux-baserade kluster.
> 
> 

![hadoop hive tez-prestandadiagram](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

För att du ska förstå Hive-frågan bättre lägger verktyget till Hive Operator-vyn i den här versionen. Du behöver bara dubbelklicka på formhörnen i jobbdiagrammet så visas alla operatorer innanför hörnet. Du kan även hovra över en viss operator för att visa mer information om denna.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Vy för uppgiftskörning för Hive-jobb på Tez
Vyn för uppgiftskörning för Hive-jobb på Tez kan användas för att hämta strukturerad och visualiserad information för Hive-jobb och få mer jobbinformation. Om det uppstår prestandaproblem kan du använda vyn för att få mer information. Du kan till exempel se hur varje uppgift fungerar och få detaljerad information om varje uppgift (data lästa/skrivna, schema/starttid/sluttid o.s.v.), så att du kan finjustera jobbkonfigurationer eller systemarkitektur baserat på den visualiserade informationen.

![Data Lake visual studio tools, vy för uppgiftskörning](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png)

## <a name="run-pig-scripts"></a>Köra Pig-skript
Data Lake-verktyg för Visual Studio har stöd för att skapa och skicka Pig-skript till HDInsight-kluster. Användare kan skapa ett Pig-projekt från en mall och skicka skript till HDInsight-kluster.

## <a name="feedbacks--known-issues"></a>Feedback och kända problem
* För närvarande visas resultaten från HiveServer2 med endast text, vilket inte är idealiskt. Vi arbetar på att åtgärda detta.
* Om resultaten startats från NULL-värden visas de inte för närvarande. Vi har löst detta och om du blockeras på grund av detta problem kan du skicka ett e-postmeddelande till oss eller kontakta supportteamet.
* Det HQL-skript som skapas av Visual Studio kodas beroende på användarens lokala regioninställning. Skriptet kanske inte kan köras korrekt om användaren överför det till klustret i binärt format.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du ansluter till HDInsight-kluster från Visual Studio med hjälp av Data Lake-verktygspaketet (HDInsight) och hur du kör en Hive-fråga. Mer information finns i:

* [Använda Hadoop Hive i HDInsight][hdinsight.hive]
* [Komma igång med Hadoop i HDInsight][hdinsight.get.started]
* [Skicka Hadoop-jobb i HDInsight][hdinsight.submit.jobs]
* [Analysera Twitter-data med Hadoop i HDInsight][hdinsight.analyze.twitter.data]

<!--Anchors-->
[Installation]: #installation
[Connect to your Azure subscription]: #connect-to-your-azure-subscription
[Navigate the linked resources]: #navigate-the-linked-resources
[Run Hive queries]: #run-hive-queries
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png


<!--Link references-->
[hdinsight-create-clusters]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight.introduction]: hdinsight-hadoop-introduction.md
[hdinsight.get.started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight.hive]: hdinsight-use-hive.md
[hdinsight.submit.jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org

