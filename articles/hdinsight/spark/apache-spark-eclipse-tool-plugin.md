---
title: 'Azure Toolkit för Eclipse: Skapa Scala-appar för HDInsight Spark'
description: Använd HDInsight Tools i Azure Toolkit for Eclipse för att utveckla Spark-program skrivna i Scala och skicka dem till ett HDInsight Spark-kluster, direkt från Eclipse IDE.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: 5ae9190213f753f8b9440ced52e4d04626af13f9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733001"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Använd Azure Toolkit för Eclipse för att skapa Apache Spark-program för ett HDInsight-kluster

Använd HDInsight Tools i Azure Toolkit for [Eclipse](https://www.eclipse.org/) för att utveckla [Apache Spark-program](https://spark.apache.org/) skrivna i [Scala](https://www.scala-lang.org/) och skicka dem till ett Azure HDInsight Spark-kluster, direkt från Eclipse IDE. Du kan använda plugin-programmet HDInsight Tools på några olika sätt:

* Så här utvecklar och skickar du ett Scala Spark-program på ett HDInsight Spark-kluster.
* Så här kommer du åt dina Azure HDInsight Spark-klusterresurser.
* Att utveckla och köra ett Scala Spark-program lokalt.

## <a name="prerequisites"></a>Krav

* Apache Spark-kluster på HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks).

* [Eclipse IDE](https://www.eclipse.org/downloads/). I den här artikeln används Eclipse IDE för Java-utvecklare.

## <a name="install-required-plug-ins"></a>Installera nödvändiga plugin-program

### <a name="install-azure-toolkit-for-eclipse"></a>Installera Azure Toolkit for Eclipse

Installationsinstruktioner finns i [Installera Azure Toolkit för Eclipse](https://docs.microsoft.com/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>Installera plugin-programmet Scala

När du öppnar Eclipse identifierar HDInsight Tools automatiskt om du har installerat Scala-plugin-programmet. Välj **OK** för att fortsätta och följ sedan instruktionerna för att installera plugin-programmet från Eclipse Marketplace. Starta om IDE:n när installationen är klar.

![Automatisk installation av Scala-plugin-programmet](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

### <a name="confirm-plug-ins"></a>Bekräfta plugin-program

1. Navigera till **Hjälp** > **Eclipse Marketplace ...**.

1. Välj fliken **Installerad.**

1. Du bör se åtminstone:
    * Azure Toolkit \<för Eclipse-versionen>.
    * Scala IDE-versionen \<>.

## <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

1. Starta Eclipse IDE.

1. Navigera till **Fönster** >  **visa andra...** > **Other...**  >  **Logga in..**.

1. Gå till **Azure** > **Azure Explorer**i dialogrutan **Visa** vy och välj sedan **Öppna**.

   ![Visa vy för Apache Spark Eclipse](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. Högerklicka på Azure-noden **Azure** i **Azure Explorer**och välj sedan **Logga in**.

1. I dialogrutan **Azure-inloggning** väljer du autentiseringsmetod, väljer **Logga in**och slutför inloggningsprocessen.

   ![Apache Spark Eclipse Azure-tecken](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. När du har loggat in visas alla **Azure-prenumerationer** som är associerade med autentiseringsuppgifterna i dialogrutan Dina prenumerationer. Stäng dialogrutan genom att trycka **på Markera.**

   ![Dialogrutan Välj prenumerationer](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. Från **Azure Explorer**navigerar du till **Azure** >  **HDInsight** för att se HDInsight Spark-kluster under din prenumeration.

   ![HDInsight Spark-kluster i Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. Du kan expandera ytterligare en klusternamnnod för att se de resurser (till exempel lagringskonton) som är associerade med klustret.

   ![Expandera ett klusternamn för att se resurser](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Länka ett kluster

Du kan länka ett normalt kluster med hjälp av det Ambari-hanterade användarnamnet. På samma sätt kan du länka för ett domänanslutet HDInsight-kluster med hjälp av domänen och användarnamnet, till exempel `user1@contoso.com`.

1. Högerklicka på **HDInsight**i **Azure Explorer**och välj Länka **ett kluster**.

   ![Azure Explorer-länkklustermeny](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Ange **klusternamn,** **användarnamn**och **lösenord**och välj sedan **OK**. Du kan också ange lagringskonto, lagringsnyckel och sedan välja Lagringsbehållare för lagringsutforskaren som ska fungera i den vänstra trädvyn

   ![Dialogrutan Länka nytt HDInsight-kluster](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > Vi använder den länkade lagringsnyckeln, användarnamnet och lösenordet om klustret både loggade in på Azure-prenumeration och länkade ett kluster.
   > ![Lagringskonton för Azure Explorer](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > När det aktuella fokuset är på **Storage Key**för endast tangentbordets användare måste du använda **Ctrl+TAB** för att fokusera på nästa fält i dialogrutan.

1. Du kan se det länkade klustret under **HDInsight**. Nu kan du skicka ett program till det länkade klustret.

   ![Azure Explorer hdi-länkat kluster](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. Du kan också ta bort länken till ett kluster från **Azure Explorer**.

   ![Olänkat kluster i Azure Explorer](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Konfigurera ett Spark Scala-projekt för ett HDInsight Spark-kluster

1. På arbetsytan Eclipse IDE väljer du **Arkiv** > **Nytt** > **Projekt...**.

1. I guiden **Nytt projekt** väljer du **HDInsight Project** > **Spark på HDInsight (Scala)**. Välj sedan **Nästa**.

   ![Välja projektet Spark på HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. Ange följande värden i dialogrutan **Nytt HDInsight Scala-projekt** och välj sedan **Nästa:**
   * Ange ett namn för projektet.
   * I **JRE-området** kontrollerar du att **ANVÄND en körningsmiljö JRE** är inställd på **JavaSE-1.7** eller senare.
   * I området **Spark-bibliotek** kan du välja **Använd Maven för att konfigurera Spark SDK-alternativet.**  Vårt verktyg integrerar rätt version för Spark SDK och Scala SDK. Du kan också välja **Alternativet Lägg till Spark SDK manuellt,** hämta och lägga till Spark SDK manuellt.

   ![Dialogrutan Nytt HDInsight Scala-projekt](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. Granska informationen i nästa dialogruta och välj sedan **Slutför**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Skapa ett Scala-program för ett HDInsight Spark-kluster

1. Expandera projektet som du skapade tidigare från **Package Explorer.** Högerklicka **src,** välj **Ny** > **annan...**.

1. I dialogrutan **Välj en guide** väljer du **Scala-guider** > **Scala-objekt**. Välj sedan **Nästa**.

   ![Markera en guide Skapa ett Scala-objekt](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)

1. Ange ett namn på objektet i dialogrutan **Skapa ny fil** och välj sedan **Slutför**. En textredigerare öppnas.

   ![Guiden Ny fil Skapa ny fil](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)

1. I textredigeraren ersätter du det aktuella innehållet med koden nedan:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Kör programmet på ett HDInsight Spark-kluster:

   a. Högerklicka på projektnamnet från Package Explorer och välj sedan **Skicka Spark-program till HDInsight**.

   b. Ange följande värden i dialogrutan **Utlösa inlämning** och välj sedan **Skicka:**

   * För **Klusternamn**väljer du det HDInsight Spark-kluster som du vill köra programmet på.
   * Välj en artefakt från Eclipse-projektet eller välj en från en hårddisk. Standardvärdet beror på vilket objekt du högerklickar på från Package Explorer.
   * I listrutan **Huvudklassnamn** visar inlämningsguiden alla objektnamn från projektet. Markera eller ange en som du vill köra. Om du har valt en artefakt från en hårddisk måste du ange huvudklassnamnet manuellt. 
   * Eftersom programkoden i det här exemplet inte kräver några kommandoradsargument eller referens-JARs eller -filer kan du lämna de återstående textrutorna tomma.

     ![Dialogrutan Apache Spark-inlämning](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. Fliken **Spark-inlämning** bör börja visa förloppet. Du kan stoppa programmet genom att välja den röda knappen i fönstret **Spark Submission.** Du kan också visa loggarna för det här specifika programmet som körs genom att välja jordglobsikonen (betecknad med den blå rutan i bilden).

   ![Fönstret Apache Spark Inlämning](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Få tillgång till och hantera HDInsight Spark-kluster med hjälp av HDInsight Tools i Azure Toolkit for Eclipse

Du kan utföra olika åtgärder med hjälp av HDInsight Tools, inklusive åtkomst till jobbutdata.

### <a name="access-the-job-view"></a>Få tillgång till jobbvyn

1. Expandera **HDInsight**i **Azure Explorer**, sedan Spark-klusternamnet och välj sedan **Jobb**.

   ![Nod för azure explorer-solförmörkelse-jobbvy](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Välj noden **Jobb.** Om Java-versionen är lägre än **1,8,** HDInsight Tools automatiskt påminnelse du installerar **E (fx) clipse** plug-in. Välj **OK** för att fortsätta och följ sedan guiden för att installera den från Eclipse Marketplace och starta om Eclipse.

   ![Installera saknade plugin E (fx)clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Öppna jobbvyn från noden **Jobb.** På den högra rutan visar fliken **Spark Job View** alla program som kördes i klustret. Välj namnet på det program som du vill visa mer information om.

   ![Information om Apache Eclipse-vyns jobbloggar](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Du kan sedan vidta någon av dessa åtgärder:

   * Hovra på jobbdiagrammet. Den visar grundläggande information om det löpande jobbet. Välj jobbdiagrammet och du kan se de faser och den information som varje jobb genererar.

     ![Apache Spark jobb diagram skede info](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Välj fliken **Logg** om du vill visa ofta använda loggar, inklusive **Driver Stderr,** **Driver Stdout**och **Directory Info**.

     ![Apache Spark Eclipse jobb logginformation](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Öppna användargränssnittet för Spark-historik och Apache Hadoop YARN -användargränssnittet (på programnivå) genom att välja hyperlänkarna högst upp i fönstret.

### <a name="access-the-storage-container-for-the-cluster"></a>Komma åt lagringsbehållaren för klustret

1. Expandera **HDInsight-noden** i Azure Explorer för att se en lista över HDInsight Spark-kluster som är tillgängliga.

1. Expandera klusternamnet för att se lagringskontot och standardlagringsbehållaren för klustret.

   ![Lagringskonto och standardlagringsbehållare](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Välj det lagringsbehållarenamn som är associerat med klustret. Dubbelklicka på mappen **VVS i** den högra rutan. Öppna en av **delfilerna** för att se utdata från programmet.

### <a name="access-the-spark-history-server"></a>Få tillgång till Spark-historikservern

1. Högerklicka på spark-klusternamnet i Azure Explorer och välj sedan **Öppna sparkhistorikgränssnittet**. När du uppmanas att ange administratörsautentiseringsuppgifterna för klustret. Du angav dessa när du etablerade klustret.

1. På instrumentpanelen för Spark-historik använder du programnamnet för att leta efter det program som du just har kört. I koden ovan anger du programnamnet `val conf = new SparkConf().setAppName("MyClusterApp")`med hjälp av . Så var ditt Spark-programnamn **MyClusterApp.**

### <a name="start-the-apache-ambari-portal"></a>Starta Apache Ambari-portalen

1. Högerklicka på Spark-klusternamnet i Azure Explorer och välj sedan **Öppna klusterhanteringsportal (Ambari)**.

1. När du uppmanas att ange administratörsautentiseringsuppgifterna för klustret. Du angav dessa när du etablerade klustret.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer

Som standard listar HDInsight Tool i Azure Toolkit for Eclipse Spark-kluster från alla dina Azure-prenumerationer. Om det behövs kan du ange de prenumerationer som du vill komma åt klustret för.

1. Högerklicka på Azure-rotnoden i Azure Explorer och välj sedan **Hantera prenumerationer**. **Azure**

1. Avmarkera kryssrutorna för den prenumeration som du inte vill komma åt i dialogrutan och välj sedan **Stäng**. Du kan också välja **Logga ut** om du vill logga ut från din Azure-prenumeration.

## <a name="run-a-spark-scala-application-locally"></a>Köra ett Spark Scala-program lokalt

Du kan använda HDInsight Tools i Azure Toolkit for Eclipse för att köra Spark Scala-program lokalt på din arbetsstation. Vanligtvis behöver dessa program inte åtkomst till klusterresurser som en lagringsbehållare, och du kan köra och testa dem lokalt.

### <a name="prerequisite"></a>Krav

När du kör det lokala Spark Scala-programmet på en Windows-dator kan du få ett undantag som förklaras i [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Det här undantaget beror på att **WinUtils.exe** saknas i Windows.

För att lösa det här felet måste [Winutils.exe](https://github.com/steveloughran/winutils) till en plats som **C:\WinUtils\bin**och sedan lägga till miljövariabeln **HADOOP_HOME** och ange variabelns värde på **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Köra ett lokalt Spark Scala-program

1. Starta Eclipse och skapa ett projekt. Gör följande i dialogrutan **Nytt projekt** och välj sedan **Nästa**.

1. I guiden **Nytt projekt** väljer du **HDInsight Project** > **Spark på HDInsight Local Run Sample (Scala)**. Välj sedan **Nästa**.

   ![Ett nytt projekt väljer en dialogruta i guiden](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Om du vill ange projektinformation följer du steg 3 till och med 6 från det tidigare avsnittet [Konfigurera ett Spark Scala-projekt för ett HDInsight Spark-kluster](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Mallen lägger till en exempelkod **(LogQuery)** under **src-mappen** som du kan köra lokalt på datorn.

   ![Plats för logquery lokala scala-program](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Högerklicka på **LogQuery.scala** och välj **Kör som** > **1 Scala-program**. Utdata som detta visas på fliken **Konsol:**

   ![Det lokala körningsresultatet för Spark-program](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Roll endast för läsare

När användare skickar jobb till ett kluster med rollbehörighet endast för läsare krävs Ambari-autentiseringsuppgifter.

### <a name="link-cluster-from-context-menu"></a>Länka kluster från snabbmenyn

1. Logga in med rollkonto med endast läsare.

2. Expandera **HDInsight** från **Azure Explorer**för att visa HDInsight-kluster som finns i din prenumeration. Klustren märkta **"Roll:Reader"** har bara rollbehörighet för läsare.

    ![HDInsight Spark-kluster i azure explorer-rollläsare](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Högerklicka på klustret med rollbehörighet för endast läsare. Välj **Länka det här klustret** från snabbmenyn för att länka klustret. Ange användarnamn och lösenord för Ambari.

    ![HDInsight Spark-kluster i Azure Explorer-länk](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Om klustret har länkats uppdateras HDInsight.
   Scenen i klustret kommer att länkas.
  
    ![HDInsight Spark-kluster i Azure Explorer länkade](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Länka klustret genom att expandera jobbnod

1. Klicka på **Jobbnod,** **fönstret Klusterjobbsåtkomst nekad.**

2. Klicka på **Länka det här klustret** om du vill länka klustret.

    ![HDInsight Spark-kluster i Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Länka kluster från fönstret Spark Submission

1. Skapa ett HDInsight-projekt.

2. Högerklicka på paketet. Välj sedan **Skicka Spark-program till HDInsight**.

   ![HDInsight Spark-kluster i Azure Explorer skickar](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Välj ett kluster som har rollbehörighet endast för läsare för **klusternamn**. Varningsmeddelandet visas. Du kan klicka på **Länka det här klustret** för att länka klustret.

   ![HDInsight Spark-kluster i Azure Explorer länkar detta](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Visa lagringskonton

* För kluster med endast läsare rollbehörighet klickar du på **Noden Lagringskonton,** **fönstret Nekad lagringsåtkomst** dyker upp.

   ![HDInsight Spark-kluster i Azure Explorer-lagring](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![HDInsight Spark-kluster i Azure Explorer nekas](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* För länkade kluster klickar du på **Noden Lagringskonton,** **fönstret Nekad lagringsåtkomst** visas.

   ![HDInsight Spark-kluster i Azure Explorer nekas2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Kända problem

När du använder **Link A Cluster,** skulle jag föreslå att du tillhandahåller autentiseringsuppgifter för lagring.

![länkkluster med solförmörkelser för lagringsautentiseringsuppgifter](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Det finns två lägen för att skicka jobben. Om lagringsautentiseringsuppgifter tillhandahålls används batch-läge för att skicka jobbet. Annars används interaktivt läge. Om klustret är upptaget kan du få felet nedan.

![eclipse få fel när klustret upptagen](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "eclipse få fel när klustret upptagen")

![eclipse få fel när kluster upptagen garn](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "eclipse få fel när kluster upptagen garn")

## <a name="see-also"></a>Se även

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använd Azure Toolkit för IntelliJ för att skapa och skicka Spark Scala-program](apache-spark-intellij-tool-plugin.md)
* [Använd Azure Toolkit för IntelliJ för att felsöka Apache Spark-program på distans via VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använd Azure Toolkit för IntelliJ för att fjärrsöka Apache Spark-program via SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
