---
title: 'Azure Toolkit för Eclipse: skapa Scala program för HDInsight Spark | Microsoft Docs'
description: Använda HDInsight Tools i Azure Toolkit för Eclipse för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster direkt från Eclipse IDE.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: nitinme
ms.openlocfilehash: cd54c4abeaa58c1b78f67c55eb5e8856dc5bb0c4
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Använda Azure Toolkit för Eclipse för att skapa Spark-program för ett HDInsight-kluster

Använda HDInsight Tools i Azure Toolkit för Eclipse för att utveckla Spark-program som skrivits i Scala och skicka dem till ett Azure HDInsight Spark-kluster direkt från Eclipse IDE. Du kan använda HDInsight Tools-plugin-programmet på några olika sätt:

* Att utveckla och skicka Scala Spark-program på ett HDInsight Spark-kluster
* Åtkomst till resurserna i Azure HDInsight Spark-kluster
* Att utveckla och köra ett Scala Spark-program lokalt

> [!IMPORTANT]
> Du kan använda verktyget för att skapa och skicka program bara för ett HDInsight Spark-kluster på Linux.
> 
> 

## <a name="prerequisites"></a>Förutsättningar

* Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit version 8, som används för Eclipse IDE-körningsmiljön. Du kan ladda ned det från den [Oracle webbplats](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Eclipse IDE. Den här artikeln använder Eclipse Neon. Du kan installera det från den [Eclipse webbplats](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Installera HDInsight-verktyg i Azure Toolkit för Eclipse och Scala plugin-program
### <a name="install-azure-toolkit-for-eclipse"></a>Installera Azure Toolkit för Eclipse
HDInsight-verktyg för Eclipse är tillgänglig som en del av Azure Toolkit för Eclipse. Installationsanvisningar finns i [installerar Azure Toolkit för Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).
### <a name="install-the-scala-plug-in"></a>Installera Scala plugin-program
När du öppnar Eclipse identifierar HDInsight verktyget automatiskt om du har installerat Scala plugin-programmet. Välj **OK** fortsätta och följ sedan anvisningarna för att installera plugin-programmet från Marketplace Eclipse.

![Automatisk installation av Scala plugin-program](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Användare kan antingen [logga in på Azure-prenumeration](#Sign-in-to-your-Azure-subscription), eller [länka ett HDInsight-kluster](#Link-a-cluster) med Ambari användarnamn/lösenord eller domän ansluten autentiseringsuppgifter för att starta. 

## <a name="sign-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration
1. Starta Eclipse IDE och öppna Utforskaren i Azure. På den **fönstret** väljer du **visa**, och välj sedan **andra**. I dialogrutan som öppnas, expandera **Azure**väljer **Azure Explorer**, och välj sedan **OK**.

   ![Visa dialogrutan vy](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Högerklicka på den **Azure** och sedan väljer **logga in**.
3. I den **Azure logga In** dialogrutan, Välj autentiseringsmetod, väljer **logga in**, och ange dina autentiseringsuppgifter för Azure.
   
   ![Azure logga In dialogrutan](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. När du har loggat in, den **Välj prenumerationer** i dialogrutan visas alla de Azure-prenumerationer som är associerade med autentiseringsuppgifter. Klicka på **Välj** att stänga dialogrutan.

   ![Dialogrutan för prenumerationer](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. På den **Azure Explorer** fliken, expandera **HDInsight** att se HDInsight Spark-kluster i din prenumeration.
   
   ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Ytterligare kan du expandera en nod i namn om du vill se de resurser (till exempel storage-konton) som associeras med klustret.
   
   ![Expandera en klusternamnet finns resurser](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

<h2 id="linkcluster">Länka ett kluster</h2>
Du kan länka en normal kluster med Ambari hanteras användarnamn, även länka ett säkerhet hadoop-kluster med hjälp av användarnamn (exempel: user1@contoso.com).
1. Klicka på **länka ett kluster** från **Azure Explorer**.

   ![länken klustrets snabbmenyn](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Ange **klusternamnet**, **användarnamn** och **lösenord**, klicka på OK för att länka klustret. Du kan också ange Lagringskonto, Lagringsnyckel och välj sedan lagringsbehållare som Lagringsutforskaren ska fungera i den vänstra trädvyn
   
   ![länka klustret dialog](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]
   > Vi använder länkade lagringsnyckel, användarnamn och lösenord om klustret både inloggad i Azure-prenumeration och länka ett kluster.
   > ![Lagringsutforskaren i Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

3. Du kan se ett länkade kluster i **HDInsight** nod när du klickar på OK-knappen, om den inkommande informationen är rätt. Nu kan du skicka ett program till den här länkade klustret.

   ![länkade kluster](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Du kan också Avlänka ett kluster från **Azure Explorer**.
   
   ![Olänkade kluster](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Ställ in ett Spark Scala-projekt för ett HDInsight Spark-kluster

1. I arbetsytan Eclipse IDE väljer **filen**väljer **ny**, och välj sedan **projekt**. 
2. Expandera i guiden Nytt projekt **HDInsight**väljer **Spark i HDInsight (Scala)**, och välj sedan **nästa**.

   ![Att välja Spark i HDInsight (Scala)-projekt](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. Guiden Skapa Scala projekt identifierar automatiskt om du har installerat Scala plugin-programmet. Välj **OK** att fortsätta att ladda ned Scala plugin-program och följ sedan anvisningarna för att starta om Eclipse.

   ![Kontrollera scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. I den **nytt projekt för HDInsight Scala** dialogrutan, ange följande värden och välj sedan **nästa**:
   * Ange ett namn för projektet.
   * I den **JRE** område, se till att **använder en körningsmiljö JRE** är inställd på **JavaSE 1.7** eller senare.
   * I den **Spark biblioteket** område, kan du välja **Använd Maven för att konfigurera Spark SDK** alternativet.  Vår verktyget integrerar rätt version för Spark SDK och Scala SDK. Du kan också välja **lägga till Spark SDK manuellt** , hämtar och lägger till Spark SDK manuellt.

   ![Dialogrutan Nytt projekt för HDInsight Scala](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. Välj i dialogrutan nästa **Slutför**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Skapa ett Scala program för ett HDInsight Spark-kluster

1. I Eclipse-IDE från paketet Explorer, expandera projektet som du skapade tidigare, högerklicka på **src**, peka på **ny**, och välj sedan **andra**.
2. I den **Välj en guide** dialogrutan Expandera **Scala guider**väljer **Scala objekt**, och välj sedan **nästa**.
   
   ![Välj en dialogruta för guiden](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. I den **Skapa ny fil** dialogrutan, ange ett namn för objektet och välj sedan **Slutför**.
   
   ![Skapa en ny fil dialogruta](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Klistra in följande kod i textredigeraren:
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
5. Kör programmet på ett HDInsight Spark-kluster:
   
   a. Högerklicka på projektnamnet paketet Explorer och markera sedan **skicka Spark-program till HDInsight**.        
   b. I den **Spark skicka** dialogrutan, ange följande värden och välj sedan **skicka**:
      
      * För **klusternamnet**, Välj HDInsight Spark-kluster som du vill köra programmet.
      * Välj en artefakt Eclipse-projektet, eller välja en från en hårddisk. Standardvärdet beror på det objekt som du högerklickar på paketet Explorer.
      * I den **Main klassnamn** listrutan skicka guiden visar alla objektnamn från projektet. Välj eller ange en som du vill köra. Om du har valt en artefakt från en hårddisk måste du ange det huvudsakliga klassnamnet manuellt. 
      * Eftersom programkoden i det här exemplet inte kräver några kommandoradsargument eller referera burkar eller filer, kan du lämna textrutorna tomt.
        
      ![Dialogrutan Skicka Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. Den **Spark skicka** fliken ska börja visas förloppet. Du kan stoppa programmet genom att välja knappen red i den **Spark skicka** fönster. Du kan också visa loggarna för specifika programmet kör genom att välja ikonen (betecknas med den blå rutan i bilden).
      
   ![Skicka Spark-fönster](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Komma åt och hantera HDInsight Spark-kluster med hjälp av HDInsight-verktyg i Azure Toolkit för Eclipse
Du kan utföra olika åtgärder med hjälp av HDInsight-verktyg, inklusive åtkomst till jobbutdata.

### <a name="access-the-job-view"></a>Åtkomst till vyn jobb
1. I Azure Explorer expanderar **HDInsight**, expandera klusternamnet Spark och välj sedan **jobb**. 

   ![Jobbet visa nod](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

2. Välj den **jobb** nod. Om Java-version som är lägre än **1.8**, HDInsight Tools automatiskt påminnelse om du installerar den **E (fx) clipse** plugin-programmet. Välj **OK** fortsätta och Följ guiden för att installera det från Marketplace Eclipse och starta om Eclipse. 

   ![Installera clipse E (fx)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. Öppna vyn jobb från de **jobb** nod. I den högra rutan i **Spark jobbet vyn** visar alla program som kördes på klustret. Välj namnet på programmet som du vill se mer information.

   ![Programinformation](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Sedan kan du göra något av följande:

   * Hovra över jobb diagrammet. Grundläggande information om jobb som körs visas. Välj jobbdiagram och du kan se steg och information som genereras av varje jobb.

     ![Steget jobbinformation](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Välj den **loggen** loggar, inklusive används för att visa ofta **drivrutinen Stderr**, **drivrutinen Stdout**, och **Directory Info**.

     ![Logginformation](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Öppna Spark historiken UI och YARN-Användargränssnittet (på programnivå) genom att välja hyperlänkar överst i fönstret.

### <a name="access-the-storage-container-for-the-cluster"></a>Åtkomst till vilken lagringsbehållare som klustret
1. I Azure Explorer expanderar den **HDInsight** rotnoden att se en lista över HDInsight Spark-kluster som är tillgängliga.
2. Expandera klusternamnet finns i lagringskontot och standardbehållare för lagring för klustret.
   
   ![Storage-konto och standard lagringsbehållare](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Välj lagring behållarens namn som är associerade med klustret. I den högra rutan, dubbelklickar du på den **HVACOut** mapp. Öppna en av de **del -** filer för att se utdata från programmet.

### <a name="access-the-spark-history-server"></a>Åtkomst till servern för Spark-historik
1. Högerklicka på klusternamnet Spark i Azure Explorer och markera **öppna Spark historik UI**. När du uppmanas ange administratörsautentiseringsuppgifterna för klustret. Du har angett dessa vid etablering av klustret.
2. I instrumentpanelen Spark historik server använder du namnet på programmet för att söka efter programmet just avslutats körs. I föregående kod, ange namnet på programmet med `val conf = new SparkConf().setAppName("MyClusterApp")`. Därför programnamnet Spark har **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Starta Ambari-portal
1. Högerklicka på klusternamnet Spark i Azure Explorer och markera **öppna klustret hanteringsportalen (Ambari)**. 
2. När du uppmanas ange administratörsautentiseringsuppgifterna för klustret. Du har angett dessa vid etablering av klustret.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer
Som standard visar HDInsight-verktyg i Azure Toolkit för Eclipse Spark-kluster från alla dina Azure-prenumerationer. Om det behövs kan du ange prenumerationer som du vill ha åtkomst till klustret. 

1. I Azure Explorer högerklickar du på den **Azure** rot nod och välj sedan **hantera prenumerationer**. 
2. Avmarkera kryssrutorna för den prenumeration som du inte vill få åtkomst till och välj sedan i dialogrutan **Stäng**. Du kan också välja **logga ut** om du vill logga ut från din Azure-prenumeration.

## <a name="run-a-spark-scala-application-locally"></a>Köra ett program med Spark Scala lokalt
Du kan använda HDInsight Tools i Azure Toolkit för Eclipse för att köra Spark Scala program lokalt på din arbetsstation. Normalt programmen behöver inte åtkomst till resurser i klustret som en lagringsbehållare och du kan köra och testa dem lokalt.

### <a name="prerequisite"></a>Krav
När du kör programmet lokalt Spark Scala på en Windows-dator, kan du få ett undantag som beskrivs i [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Det här undantaget inträffar eftersom **WinUtils.exe** saknas i Windows. 

Lös felet [ladda ned den körbara filen](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats som **C:\WinUtils\bin**, och Lägg sedan till miljövariabeln **HADOOP_HOME** och ange värdet för variabeln ska **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Kör ett lokalt Spark Scala-program
1. Starta Eclipse och skapa ett projekt. I den **nytt projekt** dialogrutan följande alternativ och välj sedan **nästa**.
   
   * Välj **HDInsight** i den vänstra fönsterrutan.
   * I den högra rutan, Välj **Spark på HDInsight lokala kör sampel (Scala)**.

   ![Dialogrutan Nytt projekt](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. Följ steg 3 till 6 för att ge projektinformationen, från det tidigare avsnittet [konfigurera ett Spark Scala-projekt för ett HDInsight Spark-kluster](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

3. Mallen lägger till en exempelkod (**LogQuery**) under den **src** mapp som du kan köra lokalt på datorn.
   
   ![Platsen för LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
4. Högerklicka på den **LogQuery** program, peka på **kör som**, och välj sedan **1 Scala program**. Utdata som visas på den **konsolen** fliken:
   
   ![Spark programmet lokala kör resultat](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Kända problem
När länkar ett kluster, jag vill föreslå du för att ange autentiseringsuppgifter för lagring.

![Interaktiv inloggning](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Det finns två lägen för att skicka jobb. Om lagring autentiseringsuppgifter tillhandahålls används batchläge för att skicka jobbet. Annars kommer att användas interaktivt läge. Om klustret är upptagen, kan du få felmeddelandet nedan.

![Eclipse uppstår fel när klustret upptagen](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![Eclipse uppstår fel när klustret upptagen](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="feedback"></a>Feedback
Skicka ett e-postmeddelande på om du har feedback eller om du stöter på andra problem när du använder det här verktyget hdivstool@microsoft.com.

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda Azure Toolkit för IntelliJ för att skapa och skicka Spark Scala-program](apache-spark-intellij-tool-plugin.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda HDInsight Tools för IntelliJ med Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

