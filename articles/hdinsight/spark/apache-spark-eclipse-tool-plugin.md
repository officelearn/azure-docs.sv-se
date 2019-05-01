---
title: 'Azure Toolkit för Eclipse: Skapa Scala-appar för HDInsight Spark '
description: Använda HDInsight-verktygen i Azure Toolkit för Eclipse för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster direkt från Eclipse IDE.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: 1ae585322316a9c215fc32cc2f8ffba2f332ff61
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704870"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Använd Azure Toolkit för Eclipse för att skapa Apache Spark-program för ett HDInsight-kluster

Använda HDInsight-verktygen i Azure Toolkit för [Eclipse](https://www.eclipse.org/) att utveckla [Apache Spark](https://spark.apache.org/) program som skrivits i [Scala](https://www.scala-lang.org/) och skicka dem till ett Azure HDInsight Spark-kluster direkt från Eclipse IDE. Du kan använda HDInsight-verktyg plugin-programmet på ett par olika sätt:

* Att utveckla och skicka in ett Scala Spark-program på ett HDInsight Spark-kluster.
* Åtkomst till dina Azure HDInsight Spark-klusterresurser.
* Att utveckla och kör ett Scala Spark-program lokalt.

> [!IMPORTANT]  
> Du kan använda det här verktyget för att skapa och skicka program bara för ett HDInsight Spark-kluster på Linux.
> 
> 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit version 8, som används för Eclipse IDE-runtime. Du kan ladda ned det från den [Oracle webbplats](https://aka.ms/azure-jdks).
* Eclipse IDE. Den här artikeln använder Eclipse Neon. Du kan installera det från den [Eclipse-webbplatsen](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Installera HDInsight-verktyg i Azure Toolkit för Eclipse och Scala plugin-programmet

### <a name="install-azure-toolkit-for-eclipse"></a>Installera Azure Toolkit för Eclipse
HDInsight Tools för Eclipse är tillgängliga som en del av Azure Toolkit för Eclipse. Installationsanvisningar finns i [installera Azure Toolkit för Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Installera Scala plugin-programmet
När du öppnar Eclipse HDInsight verktyget identifierar automatiskt om du har installerat Scala plugin-programmet. Välj **OK** att fortsätta och följ sedan anvisningarna för att installera plugin-programmet i Eclipse Marketplace.

![Automatisk installation av Scala plugin-programmet](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Användaren kan antingen [logga in på Azure-prenumeration](#sign-in-to-your-azure-subscription), eller [länka ett HDInsight-kluster](#link-a-cluster) med Ambari användarnamn/lösenord eller domänanslutna autentiseringsuppgift som ska starta. 

## <a name="sign-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration
1. Starta Eclipse IDE och öppna Azure Explorer. På den **fönstret** menyn och välj **Show View**, och välj sedan **andra**. I dialogrutan som öppnas, expandera **Azure**väljer **Azure Explorer**, och välj sedan **OK**.

   ![Dialogrutan Visa vy](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. Högerklicka på den **Azure** noden och välj sedan **logga in**.
1. I den **Azure-inloggning** dialogrutan rutan, Välj autentiseringsmetod, markera **logga in**, och ange dina autentiseringsuppgifter för Azure.
   
   ![Azure-inloggning dialogrutan](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. När du har loggat in, den **Välj prenumerationer** dialogrutan visar en lista över alla Azure-prenumerationer som är associerade med autentiseringsuppgifterna. Klicka på **Välj** att stänga dialogrutan.

   ![Dialogrutan för prenumerationer](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. På den **Azure Explorer** fliken, expandera **HDInsight** att se HDInsight Spark-kluster i din prenumeration.
   
   ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. Ytterligare kan du expandera en klusternod namn om du vill se de resurser (till exempel lagringskonton) som är kopplat till klustret.
   
   ![Expandera ett klusternamn att se resurser](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Länka ett kluster
Du kan länka ett normalt kluster med hjälp av Ambari hanteras användarnamnet. På samma sätt för ett domänanslutet HDInsight-kluster du kan länka med hjälp av domänen och användarnamnet, till exempel user1@contoso.com.

1. Välj **länka ett kluster** från **Azure Explorer**.

   ![länken snabbmenyn för kluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Ange **klusternamnet**, **användarnamn** och **lösenord**, klicka sedan på OK för att länka kluster. Alternativt kan du ange Storage-konto, Lagringsnyckeln och välj sedan storage explorer för att arbeta i den vänstra trädvyn Storage-behållare
   
   ![länka kluster dialog](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Vi använder länkade lagringsnyckel, användarnamn och lösenord om klustret både loggas i Azure-prenumeration och länkad ett kluster.
   > ![Storage explorer i Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Du kan se ett länkade kluster i **HDInsight** nod när du klickar på OK-knapp om informationen om indata är rätt. Nu kan du skicka ett program till den här länkade kluster.

   ![länkade kluster](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Du kan också Avlänka ett kluster från **Azure Explorer**.
   
   ![ta bort kopplingen kluster](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Ställ in ett Scala Spark-projekt för ett HDInsight Spark-kluster

1. I arbetsytan Eclipse IDE väljer **filen**väljer **New**, och välj sedan **projekt**. 
1. I guiden Nytt projekt expanderar **HDInsight**väljer **Spark i HDInsight (Scala)**, och välj sedan **nästa**.

   ![Att välja Spark i HDInsight (Scala)-projekt](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. Guiden Scala projekt skapa identifierar automatiskt om du har installerat Scala plugin-programmet. Välj **OK** att fortsätta att ladda ned Scala plugin-programmet och följ sedan anvisningarna för att starta om Eclipse.

   ![Kontroll av Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. I den **nytt projekt i HDInsight-Scala** dialogrutan Ange följande värden och välj sedan **nästa**:
   * Ange ett namn för projektet.
   * I den **JRE** området, se till att **använder en körningsmiljö JRE** är inställd på **JavaSE 1.7** eller senare.
   * I den **Spark biblioteket** området som du kan välja **Använd Maven för att konfigurera Spark SDK** alternativet.  Vårt verktyg integreras rätt version för Spark-SDK och Scala-SDK. Du kan också välja **manuellt lägga till Spark SDK** , ladda ned och lägger till Spark-SDK med manuellt.

   ![Dialogrutan Nytt projekt för HDInsight Scala](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. I nästa ruta i dialogrutan Välj **Slutför**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Skapa ett Scala-program för ett HDInsight Spark-kluster

1. I Eclipse IDE i Package Explorer expanderar du det projekt som du skapade tidigare, högerklicka på **src**, peka på **New**, och välj sedan **andra**.
1. I den **Välj en guide** dialogrutan Expandera **Scala guider**väljer **Scala objekt**, och välj sedan **nästa**.
   
   ![Välj en dialogruta](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. I den **Skapa ny fil** dialogrutan, ange ett namn för objektet och välj sedan **Slutför**.
   
   ![Skapa ny fil](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
1. Klistra in följande kod i textredigeraren:
   
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
1. Kör programmet på ett HDInsight Spark-kluster:
   
   a. Högerklicka på projektnamnet i Package Explorer och välj sedan **skicka Spark-program till HDInsight**.        
   b. I den **Spark bidrag** dialogrutan Ange följande värden och välj sedan **skicka**:
      
   * För **klusternamnet**, Välj HDInsight Spark-klustret som du vill köra ditt program.
   * Välj en artefakt från Eclipse-projektet, eller välja en från en hårddisk. Standardvärdet beror på vilket objekt du högerklickar på från Package Explorer.
   * I den **Main klassnamn** listrutan skicka guiden visar alla objektnamn från ditt projekt. Välj eller ange en som du vill köra. Om du har valt en artefakt från en hårddisk måste du ange det huvudsakliga klassnamnet manuellt. 
   * Eftersom programkoden i det här exemplet inte kräver några kommandoradsargument eller referera till JAR-filer eller filer, kan du lämna textrutorna tomt.
        
     ![Dialogrutan för Spark-bidrag](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. Den **Spark bidrag** fliken ska börja Visa förloppet. Du kan stoppa programmet genom att välja den röda knappen i den **Spark bidrag** fönster. Du kan också visa loggarna för specifika programmet körs genom att välja ikonen (enligt den blå rutan i bilden).
      
   ![Spark-bidrag fönster](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Komma åt och hantera HDInsight Spark-kluster med hjälp av HDInsight-verktyg i Azure Toolkit för Eclipse
Du kan utföra olika åtgärder med hjälp av HDInsight-verktyg, inklusive åtkomst till jobbutdata.

### <a name="access-the-job-view"></a>Få åtkomst till jobbvyn
1. I Azure-Utforskaren expanderar **HDInsight**, expandera klusternamnet Spark och välj sedan **jobb**. 

   ![Jobbet visa nod](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. Välj den **jobb** noden. Om Java-versionen är lägre än **1.8**, HDInsight Tools automatiskt påminnelse som du installerar den **E (fx) clipse** plugin-programmet. Välj **OK** att fortsätta och Följ guiden för att installera det från Marketplace för Eclipse och starta om Eclipse. 

   ![Installera E (fx) clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Öppna vyn jobb från den **jobb** noden. I den högra rutan i **Spark Jobbvy** fliken visar alla program som körs i klustret. Välj namnet på programmet som du vill se mer information.

   ![Programinformation](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Du kan sedan utföra någon av dessa åtgärder:

   * Hovra över jobbdiagram. Den visar grundläggande information om det pågående jobbet. Välj jobbdiagram och du kan se faser och information som alla jobb genererar.

     ![Information om steg](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Välj den **Log** fliken för att visa ofta används loggar, inklusive **drivrutinen Stderr**, **drivrutinen Stdout**, och **Directory Info**.

     ![Logginformation](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Öppna Spark historiken Användargränssnittet och Apache Hadoop YARN-Gränssnittet (på programnivå) genom att välja hyperlänkarna överst i fönstret.

### <a name="access-the-storage-container-for-the-cluster"></a>Få åtkomst till behållaren för klustret
1. I Azure Explorer, expandera den **HDInsight** rotnoden att se en lista med HDInsight Spark-kluster som är tillgängliga.
1. Expandera klusternamnet finns i lagringskontot och standardbehållare för lagring för klustret.
   
   ![Storage-konto och standard storage-behållare](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
1. Välj namnet på lagringsbehållaren kopplat till klustret. I den högra rutan, dubbelklickar du på den **HVACOut** mapp. Öppna en av de **del -** filer att se utdata från programmet.

### <a name="access-the-spark-history-server"></a>Access Spark-historikserver
1. Högerklicka på ditt namn för Spark-kluster i Azure Explorer och välj sedan **öppna Användargränssnittet för Spark-historik**. När du uppmanas, ange administratörsautentiseringsuppgifterna för klustret. Du har angett dessa medan du etablera klustret.
1. I instrumentpanelen Spark historik server använder du namnet på programmet för att söka efter programmet att du bara körts. I föregående kod, ange namnet på programmet med hjälp av `val conf = new SparkConf().setAppName("MyClusterApp")`. Därför programnamnet Spark har **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Starta Apache Ambari-portalen
1. Högerklicka på ditt namn för Spark-kluster i Azure Explorer och välj sedan **öppna klustret hanteringsportalen (Ambari)**. 
1. När du uppmanas, ange administratörsautentiseringsuppgifterna för klustret. Du har angett dessa medan du etablera klustret.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer
Som standard visas HDInsight-verktyg i Azure Toolkit för Eclipse Spark-kluster från alla dina Azure-prenumerationer. Om det behövs kan du ange de prenumerationer som du vill få åtkomst till klustret. 

1. Azure Explorer, högerklicka på den **Azure** root node och välj sedan **hantera prenumerationer**. 
1. Avmarkera kryssrutorna för den prenumeration som du inte vill få åtkomst till och välj sedan i dialogrutan **Stäng**. Du kan också välja **logga ut** om du vill logga ut från din Azure-prenumeration.

## <a name="run-a-spark-scala-application-locally"></a>Kör ett Scala Spark-program lokalt
Du kan använda HDInsight-verktyg i Azure Toolkit för Eclipse för att köra Spark Scala-appar lokalt på din arbetsstation. Normalt programmen behöver inte åtkomst till klusterresurserna, till exempel en lagringsbehållare och du kan köra och testa dem lokalt.

### <a name="prerequisite"></a>Krav
När du använder den lokala Spark Scala-appen på en Windows-dator kan du få ett undantag som beskrivs i [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Det här undantaget inträffar eftersom **WinUtils.exe** saknas i Windows. 

Du behöver för att lösa det här felet, [ladda ned den körbara filen](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats som **C:\WinUtils\bin**, och Lägg sedan till miljövariabeln **HADOOP_HOME** och ange värdet för variabeln för **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Köra ett lokalt Spark Scala-program
1. Starta Eclipse och skapa ett projekt. I den **nytt projekt** dialogrutan gör du följande val och välj sedan **nästa**.
   
   * Välj **HDInsight** i den vänstra fönsterrutan.
   * I den högra rutan, väljer **Spark på HDInsight lokala kör exemplet (Scala)**.

   ![Dialogrutan Nytt projekt](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. För att ge projektinformation, Följ steg 3 till 6 från det tidigare avsnittet [Ställ in ett Scala Spark-projekt för ett HDInsight Spark-kluster](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Mallen lägger till en exempelkod (**LogQuery**) under den **src** mapp som du kan köra lokalt på datorn.
   
   ![Platsen för LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. Högerklicka på den **LogQuery** program, peka på **kör som**, och välj sedan **1 Scala program**. Utdata som det visas på den **konsolen** fliken:
   
   ![Spark-programmet lokala Körningsresultat](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Kända problem
När länkar ett kluster, jag vill föreslå dig med autentiseringsuppgifterna för lagring.

![Interaktiv inloggning](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Det finns två lägen för att skicka jobb. Om storage-autentiseringsuppgifter har angetts och används batch-läge för att skicka jobbet. I annat fall används interaktivt läge. Om klustret är upptagen, kan du få felet nedan.

![Eclipse får fel när klustret upptagen](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![Eclipse får fel när klustret upptagen](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="feedback"></a>Feedback
Om du har feedback, eller om du stöter på andra problem när du använder det här verktyget, skicka oss ett e-postmeddelande på hdivstool@microsoft.com.

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: Utföra interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använd Azure Toolkit för IntelliJ för att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Felsöka Apache Spark-program via fjärranslutning via VPN med hjälp av Azure Toolkit för IntelliJ](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Felsöka Apache Spark-program via fjärranslutning via SSH med hjälp av Azure Toolkit för IntelliJ](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda HDInsight Tools för IntelliJ med begränsat Hortonworks-läge](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>För att hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

