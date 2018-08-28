---
title: 'Azure Toolkit för IntelliJ: skapa Spark-program för ett HDInsight-kluster '
description: Använd Azure Toolkit för IntelliJ för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/25/2017
ms.author: maxluk
ms.openlocfilehash: ed0118584d51f08d64a88dc1e7e6e2ba5f95cb0a
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042599"
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Använd Azure Toolkit för IntelliJ för att skapa Spark-program för ett HDInsight-kluster

Använd Azure Toolkit för IntelliJ plugin-programmet för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster direkt från IntelliJ integrated development environment (IDE). Du kan använda plugin-programmet på flera sätt:

* Utveckla och skicka in ett Scala Spark-program på ett HDInsight Spark-kluster.
* Åtkomst till dina Azure HDInsight Spark-klusterresurser.
* Utveckla och kör ett Scala Spark-program lokalt.

För att skapa ditt projekt, visa den [skapa Spark-program med Azure Toolkit för IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) video.

> [!IMPORTANT]
> Du kan använda det här plugin-programmet för att skapa och skicka program bara för ett HDInsight Spark-kluster på Linux.
> 

## <a name="prerequisites"></a>Förutsättningar

- Ett Apache Spark-kluster i HDInsight Linux. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Oracle Java Development Kit. Du kan installera det från den [Oracle webbplats](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. Den här artikeln använder version 2017.1. Du kan installera det från den [JetBrains webbplats](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Installera Azure Toolkit för IntelliJ
Installationsanvisningar finns i [installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Kom igång
Användaren kan antingen [logga in på Azure-prenumeration](#sign-in-to-your-azure-subscription), eller [länka ett HDInsight-kluster](#link-a-cluster) med Ambari användarnamn/lösenord eller domänanslutna autentiseringsuppgift som ska starta.


## <a name="sign-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration

1. Starta IntelliJ IDE och öppna Azure Explorer. På den **visa** menyn och välj **verktyget Windows**, och välj sedan **Azure Explorer**.
       
   ![Azure Explorer-länk](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. Högerklicka på den **Azure** noden och välj sedan **logga In**.

1. I den **Azure-inloggning** dialogrutan **logga in**, och ange dina autentiseringsuppgifter för Azure.

    ![Dialogrutan Azure-inloggning](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. När du har loggat in, den **Välj prenumerationer** dialogrutan visar en lista över alla Azure-prenumerationer som är associerade med autentiseringsuppgifterna. Välj den **Välj** knappen.

    ![Dialogrutan Välj prenumerationer](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. På den **Azure Explorer** fliken, expandera **HDInsight** visa HDInsight Spark-kluster som finns i din prenumeration.
   
    ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. Du kan ytterligare expandera en nod för klustrets namn om du vill visa de resurser (till exempel lagringskonton) som är associerade med klustret.
   
    ![En expanderad klusternamnet nod](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Länka ett kluster
Du kan länka ett normalt HDInsight-kluster med hjälp av Ambari hanteras användarnamnet. På samma sätt för ett domänanslutet HDInsight-kluster du kan länka med hjälp av domänen och användarnamnet, till exempel user1@contoso.com.

1. Välj **länka ett kluster** från **Azure Explorer**.

   ![länken snabbmenyn för kluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)


1. Ange **klusternamn**, **användarnamn** och **lösenord**. Du måste kontrollera användarnamnet och lösenordet om autentisering misslyckades. Du kan också lägga till Storage-konto, Lagringsnyckeln, och välj sedan en behållare från Storage-behållare. Information om är så att Lagringsutforskaren i det vänstra trädet
   
   ![länka kluster dialog](./media/apache-spark-intellij-tool-plugin/link-a-cluster-dialog.png)

   > [!NOTE]
   > Vi använder länkade lagringsnyckel, användarnamn och lösenord om klustret både loggas i Azure-prenumeration och länkad ett kluster.
   > ![Storage explorer i IntelliJ](./media/apache-spark-intellij-tool-plugin/storage-explorer-in-IntelliJ.png)

   
1. Du kan se ett länkade kluster i **HDInsight** noden om informationen om indata är rätt. Nu kan du skicka ett program till den här länkade kluster.

   ![länkade kluster](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Du kan också Avlänka ett kluster från **Azure Explorer**.
   
   ![ta bort kopplingen kluster](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Kör ett Scala Spark-program på ett HDInsight Spark-kluster

1. Starta IntelliJ IDEA och skapa sedan ett projekt. Gör följande i dialogrutan **Nytt projekt**: 

   a. Välj **HDInsight** > **Spark i HDInsight (Scala)**.

   b. I listan med **byggverktyg** väljer du något av följande enligt dina behov:

      * **Maven**, för guidestöd när du skapar Scala-projekt
      * **SBT**, för att hantera beroenden när du skapar Scala-projekt

    ![Dialogrutan Nytt projekt](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. Välj **Nästa**.

1. Guiden för att skapa Scala-projekt identifierar automatiskt om du har installerat Scalas plugin-program. Välj **Installera**.

   ![Kontroll av Scalas plugin-program](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Om du vill ladda ned Scalas plugin-program väljer du **OK**. Följ anvisningarna för att starta om IntelliJ. 

   ![Dialogrutan för att installera Scalas plugin-program](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. Gör följande i fönstret **Nytt projekt**:  

    ![Välj Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Ange ett projektnamn och en plats.

   b. I listrutan **Projekt-SDK** väljer du **Java 1.8** för Spark 2.x-klustret, eller **Java 1.7** för Spark 1.x-klustret.

   c. I listrutan **Spark-version** integrerar guiden för att skapa Scala-projekt rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.0.2 (Scala 2.11.8)**.

1. Välj **Slutför**.

1. Spark-projekt skapar automatiskt en artefakt. Om du vill visa artefakten, gör du följande:

   a. På den **filen** menyn och välj **projektstruktur**.

   b. I den **projektstruktur** dialogrutan **artefakter** att visa den standard-artefakt som har skapats. Du kan också skapa egna artefakt genom att välja plus-tecknet (**+**).

      ![Artefakten information i dialogrutan](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. Lägg till din programkällkod genom att göra följande:

   a. I Project Explorer högerklickar du på **src**, peka på **New**, och välj sedan **Scala klass**.
      
      ![Kommandon för att skapa en Scala-klass från Project Explorer](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. I den **Skapa ny klass med Scala** dialogrutan rutan, ange ett namn, Välj **objekt** i den **typ** och väljer sedan **OK**.
      
      ![Skapa ny klass med Scala-dialogrutan](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. I den **MyClusterApp.scala** fil, klistra in följande kod. Koden läser data från HVAC.csv (tillgängligt i alla HDInsight Spark-kluster), hämtar de rader som innehåller endast en siffra i den sjunde kolumnen i CSV-filen och skriver utdata till **/HVACOut** under standardbehållare för lagring för klustret.

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

1. Kör programmet på ett HDInsight Spark-kluster genom att göra följande:

   a. Högerklicka på projektnamnet i Projektutforskaren och välj sedan **skicka Spark-program till HDInsight**.
      
      ![Spark-programmet skicka till HDInsight-kommando](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Du uppmanas att ange dina autentiseringsuppgifter för Azure-prenumeration. I den **Spark bidrag** dialogrutan Ange följande värden och välj sedan **skicka**.
      
      * För **Spark-kluster (endast Linux)**, Välj HDInsight Spark-klustret som du vill köra ditt program.

      * Välj en artefakt från projektet IntelliJ eller välj en från hårddisken.

      * I den **Main klassnamn** väljer du de tre punkterna (**...** ), Välj huvudklass i din programkällkod och välj sedan **OK**.

        ![Dialogrutan Välj Main-klass](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Eftersom programkoden i det här exemplet inte kräver kommandoradsargument eller referera till JAR-filer eller filer, kan du lämna rutorna återstående tomt. När du har angett all information bör i dialogrutan likna följande bild.
        
        ![Dialogrutan Skicka Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. Den **Spark bidrag** flik längst ned i fönstret ska börja Visa förloppet. Du kan också stoppa programmet genom att välja den röda knappen i den **Spark bidrag** fönster.
      
     ![Fönstret Spark bidrag](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Läs hur du kommer åt jobbutdata i den ”åtkomst och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit för IntelliJ” senare i den här artikeln.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Felsöka Spark-program lokalt eller via fjärranslutning på ett HDInsight-kluster 
Vi rekommenderar också ett annat sätt för att skicka in Spark-programmet till klustret. Du kan göra det genom att ange parametrarna i den **kör/Debug konfigurationer** IDE. Mer information finns i [felsöka Spark-program lokalt eller via fjärranslutning på ett HDInsight-kluster med Azure Toolkit för IntelliJ genom SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Komma åt och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit för IntelliJ
Du kan utföra olika åtgärder med hjälp av Azure Toolkit för IntelliJ.

### <a name="access-the-job-view"></a>Få åtkomst till jobbvyn
1. I Azure-Utforskaren expanderar **HDInsight**, expandera klusternamnet Spark och välj sedan **jobb**.  

    ![Jobbet visa nod](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. I den högra rutan i **Spark Jobbvy** fliken visar alla program som körs i klustret. Välj namnet på programmet som du vill se mer information.

    ![Programinformation](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Obs!
    >

1. Hovra över jobbdiagram om du vill visa grundläggande information om som körs. Välj en nod på jobbdiagram om du vill visa faser graph och information som alla jobb genererar.

    ![Information om steg](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. Visa ofta används loggfiler, till exempel *drivrutinen Stderr*, *drivrutinen Stdout*, och *Directory information*väljer den **Log** fliken.

    ![Logginformation](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. Du kan också visa historik för Spark Användargränssnittet och YARN-Användargränssnittet (på programnivå) genom att välja en länk längst ned i fönstret.

### <a name="access-the-spark-history-server"></a>Access Spark-historikserver
1. I Azure-Utforskaren expanderar **HDInsight**, högerklickar du på klusternamnet Spark och välj sedan **öppna Användargränssnittet för Spark-historik**. 

1. När du uppmanas ange administratörsautentiseringsuppgifter för klustrets och som du angav när du ställer in i klustret.

1. Du kan använda namnet på programmet för att söka efter programmet att du bara körts på instrumentpanelen för Spark historik server. I föregående kod, ange namnet på programmet med hjälp av `val conf = new SparkConf().setAppName("MyClusterApp")`. Programnamnet Spark är därför **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Starta Ambari-portalen
1. I Azure-Utforskaren expanderar **HDInsight**, högerklickar du på klusternamnet Spark och välj sedan **öppna klustret hanteringsportalen (Ambari)**. 

1. När du uppmanas, ange administratörsautentiseringsuppgifterna för klustret. Du har angett autentiseringsuppgifterna under klusterkonfigurationen.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer
Som standard visar Azure Toolkit för IntelliJ Spark-kluster från alla dina Azure-prenumerationer. Om det behövs kan du ange de prenumerationer som du vill komma åt. 

1. Azure Explorer, högerklicka på den **Azure** root node och välj sedan **hantera prenumerationer**. 

1. I dialogrutan, avmarkerar du kryssrutorna bredvid de prenumerationer som du inte vill få åtkomst till och välj sedan **Stäng**. Du kan också välja **logga ut** om du vill logga ut från din Azure-prenumeration.

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konvertera befintliga IntelliJ IDEA-programmen kan använda Azure Toolkit för IntelliJ
Du kan konvertera befintliga Spark Scala program som du skapade i IntelliJ IDEA till att vara kompatibel med Azure Toolkit för IntelliJ. Du kan sedan använda plugin-programmet för att skicka program till ett HDInsight Spark-kluster.

1. Öppna filen associerade .iml för ett befintligt Spark Scala-program som har skapats via IntelliJ IDEA.

1. I roten nivån är en **modulen** elementet enligt följande:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Redigera elementet så att lägga till `UniqueKey="HDInsightTool"` så att den **modulen** ser ut så här:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Spara ändringarna. Ditt program bör nu vara kompatibla med Azure Toolkit för IntelliJ. Du kan testa den genom att högerklicka på projektnamnet i Project Explorer. Snabbmenyn har nu alternativet **skicka Spark-program till HDInsight**.

## <a name="troubleshooting"></a>Felsökning

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Fel vid lokal körning: *Använd en större heap-storlek*
Om du använder en 32-bitars Java SDK vid lokal körning i Spark 1.6 kan du uppstå följande fel:

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

Dessa fel inträffa heap-storleken inte är är tillräckligt stort för Spark kan köras. Spark kräver minst 471 MB. (Mer information finns i [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) En enkel lösning är att använda en 64-bitars Java SDK. Du kan också ändra inställningarna för JVM i IntelliJ genom att lägga till följande alternativ:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Att lägga till alternativ i rutan ”alternativ för virtuell dator” i IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
När länkar ett kluster, jag vill föreslå dig med autentiseringsuppgifterna för lagring.

![Länka kluster, ange autentiseringsuppgifter för lagring](./media/apache-spark-intellij-tool-plugin/link-cluster-with-storage-credential-intellij.png)

Det finns två lägen för att skicka jobb. Om storage-autentiseringsuppgifter har angetts och används batch-läge för att skicka jobbet. I annat fall används interaktivt läge. Om klustret är upptagen, kan du få felet nedan.

![Intellij får fel när klustret upptagen](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij får fel när klustret upptagen](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Feedback och kända problem
För närvarande stöds visa Spark utdata direkt inte.

Om du har förslag eller feedback, eller om du stöter på problem när du använder det här plugin-programmet, maila hdivstool@microsoft.com.

## <a name="seealso"></a>Nästa steg
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Skapa Scala-projekt (video): [skapa Spark Scala-appar](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärrfelsök (video): [Använd Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning på HDInsight-kluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: använda Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Felsöka Spark-program via fjärranslutning via VPN med hjälp av Azure Toolkit för IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Felsöka Spark-program via fjärranslutning via SSH med hjälp av Azure Toolkit för IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda HDInsight Tools för IntelliJ med begränsat Hortonworks-läge](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](apache-spark-eclipse-tool-plugin.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>För att hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
