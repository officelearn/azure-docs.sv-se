---
title: "Azure Toolkit för IntelliJ: skapa Spark-program för ett HDInsight-kluster | Microsoft Docs"
description: "Använd Azure-verktygen för IntelliJ för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: nitinme
ms.openlocfilehash: 82683349f3e562be5ac89ade4143588283abd71c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Använda Azure Toolkit för IntelliJ för att skapa Spark-program för ett HDInsight-kluster

Använda Azure Toolkit för IntelliJ plugin-programmet för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster direkt från IntelliJ integrerad utvecklingsmiljö (IDE). Du kan använda plugin-programmet på flera sätt:

* Utveckla och skicka Scala Spark-program på ett HDInsight Spark-kluster.
* Åtkomst till resurserna i Azure HDInsight Spark-kluster.
* Utveckla och köra ett Scala Spark-program lokalt.

Skapa projektet genom att visa den [skapa Spark-program med Azure Toolkit för IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) video.

> [!IMPORTANT]
> Du kan använda plugin-modulen för att skapa och skicka program bara för ett HDInsight Spark-kluster på Linux.
> 

## <a name="prerequisites"></a>Krav

- Ett Apache Spark-kluster i HDInsight Linux. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Oracle Java Development Kit. Du kan installera det från den [Oracle webbplats](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. Den här artikeln använder version 2017.1. Du kan installera det från den [JetBrains webbplats](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Installera Azure Toolkit för IntelliJ
Installationsanvisningar finns i [installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="sign-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration

1. Starta IntelliJ IDE och öppna Utforskaren i Azure. På den **visa** väljer du **verktyget Windows**, och välj sedan **Azure Explorer**.
       
   ![Länken Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Högerklicka på den **Azure** och sedan väljer **logga In**.

3. I den **Azure logga In** dialogrutan **logga in**, och ange dina autentiseringsuppgifter för Azure.

    ![Dialogrutan Azure logga In](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. När du har loggat in, den **Välj prenumerationer** i dialogrutan visas alla de Azure-prenumerationer som är associerade med autentiseringsuppgifter. Välj den **Välj** knappen.

    ![Dialogrutan Välj prenumerationer](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. På den **Azure Explorer** fliken, expandera **HDInsight** att visa HDInsight Spark-kluster som finns i din prenumeration.
   
    ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Om du vill visa de resurser (till exempel lagringskonton) som är associerade med klustret kan du ytterligare expandera en nod i klustret-namn.
   
    ![En expanderad klusternamnet nod](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Kör en Spark Scala på ett HDInsight Spark-kluster

1. Starta IntelliJ IDEA och sedan skapa ett projekt. I den **nytt projekt** dialogrutan Gör följande: 

   a. Välj **HDInsight** > **Spark i HDInsight (Scala)**.

   b. I den **Build verktyget** väljer du något av följande enligt dina behov:

      * **Maven**, Scala skapa projekt guiden support
      * **Segregerade Barlasttankar**, för att hantera beroenden och skapa för Scala-projekt

    ![Dialogrutan Nytt projekt](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. Välj **nästa**.

3. Guiden Skapa projekt Scala identifierar automatiskt om du har installerat Scala plugin-programmet. Välj **installera**.

   ![Kontroll av Scala plugin-program](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. Om du vill hämta Scala plugin-program, Välj **OK**. Följ instruktionerna för att starta om IntelliJ. 

   ![Dialogrutan Scala plugin-programmet för installation](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. I den **nytt projekt** fönster, gör du följande:  

    ![Att välja Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Ange ett namn och plats.

   b. I den **projekt SDK** listrutan, Välj **Java 1.8** för 2.x Spark-kluster eller välj **Java 1.7** för 1.x Spark-klustret.

   c. I den **Spark version** listrutan Scala guide för att skapa projektet integreras rätt version för Spark SDK och Scala SDK. Om den Spark-kluster av versionen är äldre än 2.0 väljer **Väck 1.x**. Annars väljer **Spark2.x**. Det här exemplet används **Spark punkt 2.0.2 (Scala 2.11.8)**.

6. Välj **Slutför**.

7. Spark-projekt skapas automatiskt en artefakt. Om du vill visa artefakten gör du följande:

   a. På den **filen** väljer du **projektstruktur**.

   b. I den **projektstruktur** dialogrutan **artefakter** att visa den standard-artefakt som har skapats. Du kan också skapa egna artefakt genom att välja på plustecknet (**+**).

      ![Artefakt information i dialogrutan](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. Lägg till din programmets källkod genom att göra följande:

   a. Högerklicka i Projektutforskaren **src**, peka på **ny**, och välj sedan **Scala klass**.
      
      ![Kommandon för att skapa en Scala-klass från Projektutforskaren](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. I den **skapa nya Scala klass** dialogrutan, ange ett namn, väljer **objekt** i den **typ** och välj sedan **OK**.
      
      ![Skapa ny Scala klass dialogruta](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. I den **MyClusterApp.scala** fil, klistra in följande kod. Kod som läser data från HVAC.csv (finns i alla HDInsight Spark-kluster), hämtar de rader som har endast en siffra i sjunde kolumn i CSV-filen och skriver utdata till **/HVACOut** under standardbehållare för lagring för klustret.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

9. Kör programmet på ett HDInsight Spark-kluster genom att göra följande:

   a. Högerklicka på projektnamnet i Projektutforskaren och välj sedan **skicka Spark-program till HDInsight**.
      
      ![Programmet skicka Spark på HDInsight-kommando](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Du uppmanas att ange dina autentiseringsuppgifter för Azure-prenumeration. I den **Spark skicka** dialogrutan, ange följande värden och välj sedan **skicka**.
      
      * För **Väck kluster (endast Linux)**, Välj HDInsight Spark-kluster som du vill köra programmet.

      * Välj en artefakt IntelliJ projektet eller välj en från hårddisken.

      * I den **Main klassnamn** väljer du de tre punkterna (**...** ), Välj den huvudsakliga klassen i din programkod för källa och väljer sedan **OK**.

        ![Dialogrutan Välj Main-klass](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Eftersom programkoden i det här exemplet inte kräver kommandoradsargument eller referera burkar eller filer, kan du lämna rutorna återstående tomt. När du har angett all information bör i dialogrutan likna följande bild.
        
        ![Dialogrutan Skicka Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. Den **Spark skicka** fliken längst ned i fönstret ska börja visas förloppet. Du kan också avbryta programmet genom att välja knappen red i den **Spark skicka** fönster.
      
      ![Fönstret Spark överföring](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Information om hur du kommer åt jobbutdata finns i ”åtkomst och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit för IntelliJ” senare i den här artikeln.

## <a name="run-or-debug-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Köra eller felsöka ett Spark Scala-program på ett HDInsight Spark-kluster
Vi rekommenderar också ett annat sätt att skicka Spark-program i klustret. Du kan göra det genom att ange parametrarna i den **kör/Debug konfigurationer** IDE. Mer information finns i [felsöka Spark-program på ett HDInsight-kluster med Azure Toolkit för IntelliJ via SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Komma åt och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit för IntelliJ
Du kan utföra olika åtgärder med hjälp av Azure Toolkit för IntelliJ.

### <a name="access-the-job-view"></a>Åtkomst till vyn jobb
1. I Azure Explorer expanderar **HDInsight**, expandera klusternamnet Spark och välj sedan **jobb**.  

    ![Jobbet visa nod](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. I den högra rutan i **Spark jobbet vyn** visar alla program som kördes på klustret. Välj namnet på programmet som du vill se mer information.

    ![Programinformation](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Hovra över jobb diagrammet om du vill visa grundläggande jobbinformation om körs. Välj en nod på jobbet diagrammet om du vill visa steg diagram och information som genereras av varje jobb.

    ![Steget jobbinformation](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Visa ofta används loggar, t.ex *drivrutinen Stderr*, *drivrutinen Stdout*, och *Directory Info*, väljer den **loggen** fliken.

    ![Logginformation](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Du kan också visa Spark historik UI och YARN-Användargränssnittet (på programnivå) genom att välja en länk längst upp i fönstret.

### <a name="access-the-spark-history-server"></a>Åtkomst till servern för Spark-historik
1. I Azure Explorer expanderar **HDInsight**, högerklickar du på klusternamnet Spark och väljer sedan **öppna Spark historik UI**. 

2. När du uppmanas ange administratörsautentiseringsuppgifter för det kluster som du angav när du skapar klustret.

3. Du kan använda namnet på programmet för att söka efter programmet just avslutats körs på instrumentpanelen Spark historik server. I föregående kod, ange namnet på programmet med `val conf = new SparkConf().setAppName("MyClusterApp")`. Programnamnet Spark är därför **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Starta Ambari-portal
1. I Azure Explorer expanderar **HDInsight**, högerklickar du på klusternamnet Spark och väljer sedan **öppna klustret hanteringsportalen (Ambari)**. 

2. När du uppmanas ange administratörsautentiseringsuppgifterna för klustret. Du har angett autentiseringsuppgifterna under klusterkonfigurationen.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer
Som standard visar Azure Toolkit för IntelliJ Spark-kluster från alla dina Azure-prenumerationer. Om det behövs kan du ange prenumerationer som du vill komma åt. 

1. I Azure Explorer högerklickar du på den **Azure** rot nod och välj sedan **hantera prenumerationer**. 

2. I dialogrutan avmarkerar du kryssrutorna bredvid de prenumerationer som du inte vill att komma åt och välj sedan **Stäng**. Du kan också välja **logga ut** om du vill logga ut från din Azure-prenumeration.

## <a name="run-a-spark-scala-application-locally"></a>Köra ett program med Spark Scala lokalt
Du kan använda Azure Toolkit för IntelliJ för att köra Spark Scala program lokalt på din arbetsstation. Programmen vanligtvis behöver inte åtkomst till resurser i klustret, till exempel behållare för lagring, och du kan köra och testa dem lokalt.

### <a name="prerequisite"></a>Krav
När du kör programmet lokalt Spark Scala på en Windows-dator, kan du få ett undantag som beskrivs i [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Undantaget inträffar eftersom WinUtils.exe saknas i Windows. 

Du löser det här felet [ladda ned den körbara filen](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats som **C:\WinUtils\bin**. Lägg sedan till miljövariabeln **HADOOP_HOME**, och ange värdet för variabeln för att **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Kör ett lokalt Spark Scala-program
1. Starta IntelliJ IDEA och skapa ett projekt. 

2. I den **nytt projekt** dialogrutan Gör följande:
   
    a. Välj **HDInsight** > **Spark på HDInsight lokala kör sampel (Scala)**.

    b. I den **Build verktyget** väljer du något av följande enligt dina behov:

      * **Maven**, Scala skapa projekt guiden support
      * **Segregerade Barlasttankar**, för att hantera beroenden och skapa för Scala-projekt

    ![Dialogrutan Nytt projekt](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

3. Välj **nästa**.
 
4. I fönstret nästa gör du följande:
   
    a. Ange ett namn och plats.

    b. I den **projekt SDK** listrutan väljer du en Java-version som är senare än version 1.7.

    c. I den **Spark Version** nedrullningsbara listan, Välj versionen av Scala som du vill använda: Scala 2.11.x för Spark 2.0 eller Scala 2.10.x för Spark 1.6.

    ![Dialogrutan Nytt projekt](./media/apache-spark-intellij-tool-plugin/Create-local-project.PNG)

5. Välj **Slutför**.

6. Mallen lägger till en exempelkod (**LogQuery**) under den **src** mapp som du kan köra lokalt på datorn.
   
    ![Platsen för LogQuery](./media/apache-spark-intellij-tool-plugin/local-app.png)

7. Högerklicka på den **LogQuery** program och välj sedan **kör 'LogQuery'**. På den **kör** fliken längst ned kan du se utdata som liknar följande:
   
   ![Spark programmet lokala kör resultat](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konvertera befintliga IntelliJ IDEA program att använda Azure Toolkit för IntelliJ
Du kan konvertera befintliga Spark Scala program som du skapade i IntelliJ IDEA till att vara kompatibel med Azure Toolkit för IntelliJ. Du kan sedan använda plugin-programmet för att skicka program till ett HDInsight Spark-kluster.

1. Öppna filen associerade .iml för ett befintligt Spark Scala program som har skapats via IntelliJ IDEA.

2. Rot är en **modulen** följande element:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Redigera elementet så att lägga till `UniqueKey="HDInsightTool"` så att den **modulen** ser ut ungefär så här:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Spara ändringarna. Ditt program bör nu vara kompatibla med Azure Toolkit för IntelliJ. Du kan testa den genom att högerklicka på projektnamnet i Projektutforskaren. Snabbmenyn har nu alternativet **skicka Spark-program till HDInsight**.

## <a name="troubleshooting"></a>Felsökning

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Fel vid lokal körning: *Använd en större heapstorlek*
I Spark 1.6 om du använder en 32-bitars Java SDK under lokal körning kan uppstå följande fel:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Dessa fel inträffa eftersom heap-storlek inte är tillräckligt stor för Spark ska köras. Spark kräver minst 471 MB. (Mer information finns i [SPARK 12081](https://issues.apache.org/jira/browse/SPARK-12081).) En enkel lösning är att använda en 64-bitars Java SDK. Du kan också ändra inställningarna för JVM i IntelliJ genom att lägga till följande alternativ:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Lägga till alternativ i rutan ”alternativ för virtuell dator” i IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
För att skicka ett program till Azure Data Lake Store, Välj **interaktiv** läge under processen för Azure-inloggning. Om du väljer **automatisk** läge, du får ett felmeddelande.

![Interaktiv inloggning](./media/apache-spark-intellij-tool-plugin/interative-signin.png)

Nu ska löste vi problemet. Du kan välja ett Azure Data Lake-kluster att skicka ditt program med valfri metod för inloggning.

## <a name="feedback-and-known-issues"></a>Feedback och kända problem
För närvarande kan stöds visa Spark utdata direkt inte.

Om du har förslag eller feedback eller om du stöter på problem när du använder plugin-modulen, skicka e-post på hdivstool@microsoft.com.

## <a name="seealso"></a>Nästa steg
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Skapa Scala projekt (video): [skapa Spark Scala-program](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärråtkomst debug (video): [Använd Azure Toolkit för IntelliJ till att felsöka Spark-program på HDInsight-kluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att skapa realtid strömmade program](apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda HDInsight Tools för IntelliJ med Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](apache-spark-eclipse-tool-plugin.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

